# Getting Started with the MAX78000FTHR

[TOC]

---

![Feather Board](img/MAX78000FTHR.jpg)

## Schematic

The schematic and BOM can be found in the MAX78000FTHR [Datasheet](https://www.analog.com/en/design-center/evaluation-hardware-and-software/evaluation-boards-kits/max78000fthr.html).

## First-time Firmware Updates

The MAX78000FTHR has an integrated MAX32625PICO ("PICO") debug adapter.  The firmware for this debugger should be updated to the latest version before development.  These updates contain bug fixes and improvements that are required to ensure proper operation of the debugger.

### Updating the MAX32625PICO ("PICO") Debug Adapter Firmware

1. Download the "max32625_max78000fthr_if_crc_v1.0.2.bin" file from [this](https://github.com/analogdevicesinc/MaximAI_Documentation/raw/master/MAX78000_Feather/MAX32625PICO_files/max32625_max78000fthr_if_crc_v1.0.2.bin) link.  

2. Connect the included micro-USB cable to the MAX78000FTHR _without_ connecting the other side of the cable to your host PC yet.

    <img src="img/FTHR_partial_connected.jpg" alt="PICO Partially Connected" width="400"/>

3. Press and hold the SW5 pushbutton.

    <img src="img/FTHR_SW5.jpg" alt="PICO Pushbutton" width="400"/>

4. _While holding down the SW5 pushbutton_ connect the other side of the micro-USB cable to your host PC.  

    Keep the pushbutton held down until the red LED (D3) on the MAX78000FTHR blinks and becomes solid.

    <img src="img/FTHR_connected.jpg" alt="PICO Connected" width="400"/>

5. A "MAINTENANCE" drive should now appear on your file system.

    <img src="img/MAINTENANCE.jpg" alt="Maintenance Drive Image" width="400"/>

    Note:  If a DAPLINK drive presents itself instead, retry the connection while holding the pushbutton down.  Holding SW5 while connecting the FTHR board will place it in MAINTENANCE mode, allowing its debugger firmware to be reprogrammed.

6. Drag and drop the "max32625_max78000fthr_if_crc_v1.0.2.bin" file onto the MAINTENANCE drive.  This will flash the "PICO" with the updated firmware.

    1. <img src="img/MAINTENANCE.jpg" alt="Maintenance Drive Image" width="400"/>

    2. <img src="img/FTHR_drag_and_drop.jpg" alt="Drag and Drop" width="400"/>

    3. <img src="img/pico_flashing.JPG" alt="Flashing" width="400"/>

7. Once the flashing is complete, the "PICO" will restart and present itself as a "DAPLINK" drive.

    <img src="img/DAPLINK.jpg" alt="Daplink Drive" width="400"/>

8. Open the DAPLINK drive.

    <img src="img/DAPLINK_opened.jpg" alt="Daplink Opened" width="400"/>

9. Open the "DETAILS.TXT" file and verify the contents match the "MAX32625PICO_files/DETAILS.TXT" file in this repository.  Specifically - the "Git SHA" field should match exactly.

    <img src="img/DETAILS_Git_SHA.jpg" alt="Details GIT SHA" width="400"/>

10. Your "PICO" debugger is now ready to use with the MAX78000FTHR.

## Software Development

### Setup

Embedded development for the MAX78000FTHR is enabled by the Maxim Microcontroller SDK (“MSDK”), now a part of Analog Devices.

See the [**MSDK User Guide**](https://analogdevicesinc.github.io/msdk/USERGUIDE/) for detailed documentation on installation, setup, and getting started.

### How to Unlock a MAX78000 That Can No Longer Be Programmed

The SWD interface is unavailable for a certain number of clock cycles after reset.  If the application code instructs the device to enter any low power or shutdown mode too soon, it could be difficult to reprogram the device.  The following instructions help recover a device in this lockout state:  

1. Remove the USB cable connected to the MAX32625PICO debug adapter board.  
2. Remove power to the target device by powering down the EV Kit or feather board.  
3. Place the MAX32625PICO debug adapter in MAINTENANCE mode by holding down its button while reconnecting the USB cable to the host PC.  

   - The MAX32625PICO debug adapter will enumerate as a mass storage device named MAINTENANCE.  
   - Drag-n-Drop the provided bin file to the drive named MAINTENANCE:  [DAPLINK bin file](https://github.com/analogdevicesinc/MaximAI_Documentation/raw/master/MAX78000_Evaluation_Kit/MAX32625PICO_files/max32625_max78000fthr_if_crc_v1.0.2.bin).  
   - Following the Drag-n-Drop, the MAX32625PICO should reboot and reconnect as a drive named DAPLINK.  

4. Make sure the 'Automation allowed' field is set to 1 in the DETAILS.TXT file on the DAPLINK drive. If not, perform the following steps:
    - Create an empty text file named '**auto_on.cfg**'. Copy the file to DAPLINK drive while its button is held.
    - Release the button when the drive unmounts. When it remounts, confirm "Automation allowed" is set to 1 in DETAILS.TXT file.
5. Create an empty text file named '**erase.act**' and Drag-and-drop it onto the DAPLINK drive.
6. This should mass erase the flash of the target device, allowing the device to be programmed again.

At this point, the target device should be once again programmable.

Note:  In order to avoid the locked out state to begin with, it is recommended that during code development, a delay be placed at the beginning of user code in order to give the debug adapter an opportunity to communicate with or halt the processor.  A delay of 2 seconds is ideal so that the debugger can be attached manually.

## Machine Learning (ML) Development

As the MAX78000 contains a powerful Convolution Neural Network (CNN) accelerator, there is also the Machine Learning side of development. This is done with a separate set of tools. The example projects that are found in the `CNN` sub-folder of the MAX78000 examples have been created with these tools. More specifically, they have been created with the `ai8x-synthesis` (“izer”) tool, which converts a trained model into C code that can be compiled and flashed onto the MAX78000.

### ML Overview

The documentation associated with the setup and usage of these tools is significant. Here are the links to get started:

* [Analog Devices AI Documentation Repository](https://github.com/analogdevicesinc/MaximAI_Documentation)
* [ai8x-synthesis tool](https://github.com/analogdevicesinc/ai8x-synthesis)
* [ai8x-training tool](https://github.com/analogdevicesinc/ai8x-training)
* [Workflow Guide](https://github.com/analogdevicesinc/MaximAI_Documentation/blob/master/Guides/MAX78000_Workflow_Guide.md#max78000-workflow-guide)

### ML Videos

A large technical library of technical training videos on Artificial Intelligence (AI) and the MAX78000/MAX78002 is available via the links below. These are the best way to get started with ML on the MAX78002.  The “Understanding Artificial Intelligence” series is highly recommended for all developers.

- **Understanding Artificial Intelligence**
  - [Episode 1 - Who Needs Machine Learning Anyways?](https://www.analog.com/en/education/education-library/videos/6313215159112.html)
  - [Episode 2 - Thinking About Machine Learning](https://www.analog.com/en/education/education-library/videos/6313214510112.html)
  - [Episode 3 - All About Models](https://www.analog.com/en/education/education-library/videos/6313216827112.html)
  - [Episode 4 - All About Training](https://www.analog.com/en/education/education-library/videos/6313215699112.html)
  - [Episode 5 - Say Hello to the MAX78000](https://www.analog.com/en/education/education-library/videos/6313217809112.html)
  - [Episode 6 - From Checkpoint to C](https://www.analog.com/en/education/education-library/videos/6313215449112.html)
  - [Episode 7 - How to Train a Network](https://www.analog.com/en/education/education-library/videos/6313213231112.html)
  - [Episode 8 - The MAX78000FTHR](https://www.analog.com/en/education/education-library/videos/6313213346112.html)
  - [Episode 8.5 - Visual Studio Code](https://www.analog.com/en/education/education-library/videos/6313212752112.html)

### ML Setup

The setup and usage of the machine learning tools is thoroughly documented in the [README.md](https://github.com/analogdevicesinc/ai8x-synthesis/blob/develop/README.md) file that can be found in the root directory of both the “izer” and training tools. See the [Installation](https://github.com/analogdevicesinc/ai8x-training/blob/develop/README.md#installation) section for detailed instructions.

### ML Usage

Detailed usage of the “izer” and training tools is beyond the scope of this document. The [Workflow Guide](https://github.com/analogdevicesinc/MaximAI_Documentation/blob/master/Guides/MAX78000_Workflow_Guide.md) is a great introduction.

Additionally, the [README](https://github.com/analogdevicesinc/ai8x-synthesis/blob/develop/README.md) contains all of the usage information for the tools.

Below are a few exercises to get started after setup is complete:

* Run the `gen-demos-max78000.sh` script found in the root directory of the “izer” tool and reference its [Command Line Arguments](https://github.com/analogdevicesinc/ai8x-synthesis#command-line-arguments-3) table to see how it works.

* Build and flash the output of one of the `gen-demos-max78000.sh` projects and verify that the generated “known answer” test passes.

* Locate the YAML files for the pre-trained models in the “izer” tool and reference the [YAML Network Description](https://github.com/analogdevicesinc/ai8x-synthesis#yaml-network-description) to see how they work.

* Run `train_all_models.sh` from the training repository and reference its [Command Line Arguments](https://github.com/analogdevicesinc/ai8x-training#command-line-arguments) to see how it works.

* Familiarize yourself with the concept of data loaders with [Application Note](https://www.analog.com/en/resources/app-notes/data-loader-design-for-max78000-model-training.html) and explore the pre-written data loaders found in the [datasets](https://github.com/analogdevicesinc/ai8x-training/tree/develop/datasets) directory of the training tool.

## TFT display

The TFT display is optional and not supplied with the MAX78000 Feather board.

The MAX78000 Feather compatible 2.4'' TFT FeatherWing display can be ordered [here](<https://learn.adafruit.com/adafruit-2-4-tft-touch-screen-featherwing>).

This TFT display comes fully assembled with dual sockets for MAX78000 Feather to plug into.

To connect TFT display to MAX78000 Feather board you need to solder two headers as shown below:

<img src="img/feather_header.png" style="zoom: 25%;" />

<img src="img/feather_TFT.png" style="zoom:25%;" />

While using TFT display keep its power switch in "ON" position. The TFT "Reset" button also can be used as Feather reset. 

### CNN Boost

The MAX78000FTHR features an external boost circuit that can be used to supply the CNN when under high computational load.  The boost circuit is enabled by  supplying the `--boost 2.5` command line argument to ai8xizer.

The internal SIMO can be used to power the CNN under moderate computational loads, however, the external boot circuit is recommended during development to avoid SIMO brown-out due to transient over-current conditions which can cause the CNN to fail.

### Links to mnist and additional CNN examples

- [mnist CNN example](https://github.com/analogdevicesinc/msdk/tree/master/Examples/MAX78000/CNN/mnist)
- [Directory of additional CNN examples](https://github.com/analogdevicesinc/msdk/tree/master/Examples/MAX78000/CNN)

### Going beyond the included CNN examples - Advanced Topics

- [AI8X Model Training and Quantization](https://github.com/analogdevicesinc/ai8x-synthesis/blob/master/README.md)
- [AI8X Network Loader and RTL Simulation Generator](https://github.com/analogdevicesinc/ai8x-synthesis/blob/master/README.md)
