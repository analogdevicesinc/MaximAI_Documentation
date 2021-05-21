# MAX78000 Camera DMA Streaming



## Overview
Capturing and storing QVGA (320x240) or VGA(640x480) images require a significant amount of memory beyond the internal memory of MAX78000. For this purpose, camera DMA streaming mode can be used to capture large images and transfer them line by line into MAX78000 memory for further CNN processing or displaying on the TFT. 

## Camera settings

To activate the camera DMA streaming mode, use `STREAMING_DMA` argument of `camera_setup()` function. An example of camera initialization code is shown below:

```c++
 int ret = 0;
 int slaveAddress;
 int id; 
 int dma_channel;

 // Initialize DMA for camera interface
 MXC_DMA_Init();
 dma_channel = MXC_DMA_AcquireChannel(); 

 // Initialize the camera driver.
 camera_init(CAMERA_FREQ);

 // Obtain the I2C slave address of the camera.
 slaveAddress = camera_get_slave_address();
 PR_DEBUG("Camera I2C slave address is %02x\n", slaveAddress);

 // Obtain the product ID of the camera.
 ret = camera_get_product_id(&id);

 if (ret != STATUS_OK) {
     PR_ERR("Error returned from reading camera id. Error %d\n", ret);
     return -1;
 }

 PR_DEBUG("Camera Product ID is %04x\n", id);

 // Obtain the manufacture ID of the camera.
 ret = camera_get_manufacture_id(&id);

 if (ret != STATUS_OK) {
     PR_ERR("Error returned from reading camera id. Error %d\n", ret);
     return -1;
 }

 PR_DEBUG("Camera Manufacture ID is %04x\n", id);

 // Setup the camera image dimensions, pixel format and data acquiring details.
 ret = camera_setup(IMAGE_XRES, IMAGE_YRES, PIXFORMAT_RGB565, FIFO_FOUR_BYTE, STREAMING_DMA, dma_channel); // RGB565 stream

 if (ret != STATUS_OK) {
     PR_ERR("Error returned from setting up camera. Error %d\n", ret);
     return -1;
 }
 // If needed, reduce speed by setting camera internal clock pre-scaler value=0x0-0x1F.
 camera_write_reg(0x11, 0x1);
```



## Camera DMA Streaming API

The DMA automatically transfers captured images from PCIF RX FIFO into two streaming  ping-pong buffers with image width size allocated in the memory. When one buffer is being accessed by the DMA, a second buffer can be consumed by the application (CNN process, TFT display). The application needs to get a pointer to the available streaming buffer by calling `get_camera_stream_buffer()` and waits until it's not NULL. After consuming the data, the application must release the streaming buffer for the next DMA transaction by calling `release_camera_stream_buffer()`function. Also, the application can monitor the number of DMA transfers (image height) per image as well as an overflow condition.

```c++
typedef struct _stream_stat {
    uint32_t dma_transfer_count;
    uint32_t overflow_count;
} stream_stat_t;

// Get camera streaming buffer.
uint8_t* get_camera_stream_buffer(void);

// Release camera streaming buffer.
void release_camera_stream_buffer(void);

// Get statistics of DMA streaming mode.
stream_stat_t* get_camera_stream_statistic(void);
```

The overflow condition is detected when `overflow_count` is not zero. It may happen if the processing of the image by the application is at a slower pace than its capturing. To match the image capturing and processing speed, increase the camera clock pre-scale value: *camera_write_reg(0x11, **0x1**)*. This value can be set in 0x0 - 0x1F range. Reducing the clock pre-scale value brings down the camera output clock and slows down the image capture speed.

## Streaming image to the CNN

The CNN model must be  synthesized with `--fifo ` or `--fast-fifo` options in ai8xize.py.

The `cnn_start()` function should be called first followed by `camera_start_capture_image()` to capture  an image. 

The CNN will start processing data automatically when enough data is available.

Loading image data to the CNN is done in a time-critical nested loop by writing image horizontal lines one by one into the CNN FIFO.

It's important to check `stat->dma_transfer_count` to make sure there is no overflow of streaming buffers. If an overflow condition is detected, increase the camera clock pre-scale value as described above.

```c++
uint32_t cnn_unload_buffer[CNN_NUM_OUTPUTS/4];
uint32_t imgLen;
uint32_t width, height;
uint8_t* data = NULL;
uint8_t* data_ptr;

// Get the details of the image from the camera driver.
camera_get_image(&data, &imgLen, &width, &height);
// Enable CNN clock
MXC_SYS_ClockEnable(MXC_SYS_PERIPH_CLOCK_CNN);
// Start CNN
cnn_start();
// Capture image
camera_start_capture_image();

// Load captured image to CNN memory
for (int i = 0; i < height; i++) {
	// Wait until camera streaming buffer is full
    while((data = get_camera_stream_buffer()) == NULL)
    {
    	if(camera_is_image_rcv())
        	break;
    };

    data_ptr = data;
	// Load image horizontal line
    for (int j = 0; j < width; j++) {
    	uint8_t ur, ug, ub;
        // Extract colors from RGB565 and convert to signed value
        ur = (*data_ptr & 0xF8) ^ 0x80; 
        ug = ((*data_ptr << 5) | ((*(data_ptr+1) & 0xE0) >> 3)) ^ 0x80;
        ub = (*(data_ptr+1) << 3) ^ 0x80;
        // Next pixel
        data_ptr += 2;
        // Wait for FIFO 0
        while (((*((volatile uint32_t*) 0x50000004) & 1)) != 0);  
        // Loading data into the CNN fifo in HWC format
        *((volatile uint32_t*) 0x50000008) = 0x00FFFFFF & ((ub << 16) | (ug << 8) | ur);
     }
    
     // Optional: Draw one horizontal line of captured image on TFT
     // It increases latency and may require reducing image capture speed 
     MXC_TFT_ShowImageCameraRGB565(X_START, Y_START + i, data, width, 1);
       
     // Release streaming buffer
     release_camera_stream_buffer();
}

stream_stat_t* stat = get_camera_stream_statistic();
PR_DEBUG("DMA transfer count = %d", stat->dma_transfer_count);
// Check overflow condition
PR_DEBUG("OVERFLOW = %d", stat->overflow_count);
    
// Wait when image is fully captured
while(camera_is_image_rcv() == 0);

// Wait for CNN done
while (cnn_time == 0) {
	__WFI();    
}
    
// Unload CNN result
cnn_unload(cnn_unload_buffer);
// Stop CNN
cnn_stop();

// Disable CNN clock to save power
MXC_SYS_ClockDisable(MXC_SYS_PERIPH_CLOCK_CNN);
```




## References
[1] https://github.com/MaximIntegratedAI/MaximAI_Documentation