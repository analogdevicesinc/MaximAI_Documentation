# Making Your Own Audio and Image Classification Application Using Keyword Spotting and Cats-vs-Dogs Examples

*MAX78000 can be used to develop a variety of AI audio and vision applications. Both MAX78000FTHR and MAX78000Evkit include a microphone as well as an image sensor.*

Building an AI application on MAX78000 includes the following steps:

1. Selection of dataset and curation

2. Creating the data loader

3. Reusing an existing model, or creating a new one

4. Training the model with the data set to achieve acceptable accuracy, storing the weights (checkpoint file)

5. Quantizing the checkpoint and evaluating the quantized checkpoint to make sure the accuracy is still acceptable

6. Creating a sample input data using the evaluation script, to be used as the “Known Answer Test” (KAT) by the synthesis tool

7. Creating or reusing a hardware description file (YAML) according to your model

8. Synthesizing the quantized checkpoint to C code (requires sample input data and the YAML file)

9. Running the generated C project on the MAX78000 which includes the KAT. The code includes weights and an API to load the input, run inference and unload the result

10. Using the generated C code as the skeleton to develop an application

There are plenty of documentation, examples, and scripts for each of the above steps to help accelerate the development cycle. For more information, please check https://github.com/analogdevicesinc/MaximAI_Documentation.

In this document, instructions are provided to create your own audio or image classifiers based on the Keyword Spotting and Cats-vs-Dogs demo examples.

**NOTE:** Training a model without CUDA hardware acceleration and GPU could take a very long time (e.g. 17 min/epoch for KWS20 and 30min/epoch for cats_vs_dogs tested on a Core-i5  PC without GPU). If a gaming PC with GPU is available, use it for training (e.g. 15× faster tested on a Core i7 laptop with GTX1660Ti GPU).

## Datasets

A number of examples are included in *ai8x-training* and *ai8x-synthesis* as references. The training script for each example calls the data loader which automatically downloads the required dataset or provides instructions for the user to download it manually if the host repository requires registration and login.

The data loader also applies preprocessing and augmentation to the raw dataset and prepares the data to be used by the training pipeline. This step is performed only once and the same processed data will be referenced in future training. Depending on the example and the dataset, this process could take from minutes to hours (e.g. 2 hours for KWS20_v3 or 10 minutes for cats_vs_dogs).

## Modifying Cats vs Dogs to Create Your Own Image Classifier

The provided model, data loader, and scripts can be used as a reference to quickly create your own image classifier. The following steps summarize the required modifications:

### 1. Dataset and Data Loader

Use **ai8x-training/datasets/cats_vs_dogs.py** in ai8x-training , rename according to your own image dataset and modify the applied augmentations as needed. Maintain the folder structure as described below for train and test and copy the training (90%) and test (10%) images to folders representing your classes.

```text
data-- cats_vs_dogs
           |--test
           |    |-- cats: includes .jpg images of cats.
           |    |-- dogs: includes .jpg images of dogs.
           |--train
                |-- cats: includes .jpg images of cats.
                |-- dogs: includes .jpg images of dogs.
```

If there are more than two classes, the model and the model description file (YAML) need to be updated. More changes may be needed if the image size is changed from 128x128.

**Make sure to remove the “augmented” folder when recreating the dataset.**

### 2. Model

Use **ai8x-training/models/ai85net-cd.py**, or modify it as needed for more classes.

### 3. Training Script

Copy **ai8x-training/scripts/train_catsdogs.sh,** update with your own model and dataset names:

```bash
(ai8x-training) $ python train.py --epochs 250 --optimizer Adam --lr 0.001 --deterministic --compress policies/schedule-catsdogs.yaml --model ai85cdnet --dataset cats_vs_dogs --confusion --param-hist --embedding --device MAX78000 "$@"
```

You can use the same learning rate policy schedule-catsdogs.yaml or recreate your own. You can also lower the number of epochs to end the training faster.

Run the training script. The unquantized checkpoint (`qat_best-q.pth.tar`) is stored in a dated folder inside `./logs` and updated per epoch. Check the accuracy of the unquantized model at the end.



### 4. Quantization Script

Copy **ai8x-synthesis/scripts/quantize_catsdogs.sh,** update with the path to the location of the `qat_best.pth.tar` checkpoint file (e.g. inside the `logs/` folder).

```bash
(ai8x-synthesis) $ python quantize.py ../ai8x-training/logs/2022.05.11-210349/qat_best.pth.tar ../ai8x-training/logs/2022.05.11-210349/qat_best-q.pth.tar --device MAX78000 -v "$@"
```

Run the quantization script `quantize.sh` to generate the quantized checkpoint **qat_best-q.pth.tar** in the destination path.



### 5. Evaluation Script

Copy **ai8x-training/scripts/evaluate_catsdogs.sh,** update with your own model, dataset and the path to the quantized checkpoint as needed:

```bash
(ai8x-training) $ python train.py --model ai85cdnet --dataset cats_vs_dogs --confusion --evaluate --exp-load-weights-from ../ai8x-training/logs/2022.05.11-210349/qat_best-q.pth.tar -8 --device MAX78000 "$@"
```



### 6. Generating Input Sample Data for Known Answer-Test (KAT)

Run the evaluation script you created in the previous step, adding **`--save-sample 1`** (or another index) to generate a `.npy` sample data for your own dataset (e.g., **ai8x-training/sample_cats_vs_dogs.npy).** Copy the generated sample input into the **ai8x-synthesis/test** folder (default location) or as needed (e.g. to the `logs/` folder with the quantized checkpoint).



### 7. YAML Model

Copy **ai8x-synthesis/networks/cats-dogs-hwc.yaml,** update with your own model and dataset.

If the model architecture or number of output classes are changed, the YAML file’s layers need to be updated accordingly.

```yaml
arch: ai85cdnet # update
dataset: cats_vs_dogs #update

# Define layer parameters in order of the layer sequence
layers:
 - pad: 1
  activate: ReLU
  out_offset: 0x1000
  processors: 0x0000000000000007
  data_format: HWC
  operation: Conv2d
  streaming: true
...
```



### 8. Creating the C Code with KAT

Copy **ai8x-synthesis/scripts/gen_catsdogs_max78000.sh,** update with your own model, YAML file and quantized checkpoint and target directory, and project name (prefix):

```bash
DEVICE="MAX78000"
TARGET="sdk/Examples/$DEVICE/CNN"
COMMON_ARGS="--device $DEVICE --compact-data --mexpress --timer 0 --display-checkpoint --verbose"

python ai8xize.py --test-dir $TARGET --prefix cats-dogs --checkpoint-file ../ai8x-training/logs/2022.05.11-210349/qat_best-q.pth.tar --config-file networks/cats-dogs-hwc.yaml --fifo --softmax $COMMON_ARGS "$@"
```

If the input sample data is not in the default **ai8x-synthesis/test** folder, add the path to the scripts well:

- **`--sample-input ../ai8x-training/logs/2022.05.11-210349/sample_cats_vs_dogs.npy`**

Run the script to generate the KAT C code in the specified target directory (e.g. `ai8x-synthesis/sdk/Examples/MAX78000/CNN/cats-dogs/`)



### 9. Building, Flashing and Running KAT Code on MAX78000 Platform

“Make” the project and load it into the device, configure and connect the serial terminal and check the messages for `PASS`:

```bash
$ cd /c/MaximSDK/Examples/MAX78000/CNN/cats-dogs
$ make -r
$ openocd -s C:/MaximSDK/Tools/OpenOCD/scripts -f interface/cmsis-dap.cfg -f target/max78000.cfg  -c "program build/cats-dogs.elf reset exit"
```



### 10. Updating the Demo Application

Copy **cats-dogs_demo,** and update as needed:

- Replace **cnn.c, cnn.h, weights.h, sampledata.h** with those generated in the C code of your own model.
- Update `main.c` with your own class names, e.g.:
  - const char classes [CNN_NUM_OUTPUTS] [10]= { "Cat", "Dog" };
- Make any other changes needed to print the correct class names.
- Build, flash and run the code. It uses the images from the camera, loads them to CNN, performs the inference, and prints the result using the serial terminal.

```bash
$ cd /c/MaximSDK/Examples/MAX78000/CNN/cats-dogs_demo
$ make -r clean
$ make -r
$ openocd -s C:/MaximSDK/Tools/OpenOCD/scripts -f interface/cmsis-dap.cfg -f target/max78000.cfg  -c "program build/max78000.elf reset exit"
```

- If you want to do the classification for the included sample data, uncomment #define USE_SAMPLEDATA in main.c.

**Note:**

- If the model is changed, the loading and unloading functions in `main.c` may need to be updated according to the autogenerated KAT code in the previous step.



## Modifying KWS to Create Your Own Keyword Spotter

The data loader (`ai8x-training/datasets/kws20.py`) automatically downloads Google’s speech command dataset. The dataset consists of over 100,000 utterances of 35 different words stored as one-second wave format files sampled at 16 kHz. For this demo, the model is trained with the 20 words shown below as desired classes, and the rest are all labeled as an “unknown” class (21 classes).

Speech command classes:

- 'backward', 'bed', 'bird', 'cat', 'dog', 'down', 'eight', 'five', 'follow', 'forward', 'four', 'go', 'happy', 'house', 'learn', 'left', 'marvin', 'nine', 'no', 'off', 'on', 'one', 'right', 'seven', 'sheila', 'six', 'stop', 'three', 'tree', 'two', 'up', 'visual', 'wow', 'yes', 'zero'

Desired classes:

- 'up', 'down', 'left', 'right', 'stop', 'go', 'yes', 'no', 'on', 'off', 'one', 'two', 'three', 'four', 'five', 'six', 'seven', 'eight', 'nine', 'zero'



### **Detecting Longer Audio Phrases (longer than 1 second) with Multiple Words **

**If you are interested in detecting longer than 1-second multi-word audio**, you can still use the same model with single words and update the kws20_demo application to detect a sequence of individual words with a simple state machine. It is **NOT recommended** to change the model and data loader from 128×128 (1-second) to higher dimensions for longer audio phrases. It will require significant changes to the code and debugging of the demo application, reduces the accuracy, and increases the response delay and false detections.



### Adding New Labels

The data loader expects 1-second 16 kHz mono audio .wav files for training and test data. You can use different utilities or scripts to record your own 1-second keywords. You can further augment with synthesized speech using text-to-speech python libraries (pyttsx3, gTTS). Generally, you need to have at least several hundred samples for a new keyword. Once you captured the new keyword samples, follow the instructions below.



#### **Recording Audio and Converting to 1-second 16 kHz Wave Files**

To record your own audio, you can use the `VoiceRecorder.py` script in `kws20_demo/Utility` and say a word many times, about one word per second with different intonation and speed and different people. Then place it in an INPUT_FOLDER and use the following script to segment it to 1-second 16 kHz sampled wav files (the `ffmpeg` package is needed):

```bash
$ python convert_segment_wav.py -i INPUT_FOLDER -o OUTPUT_FOLDER
```

You can also use the **speech_ptch_change.py** to further augment your 1-second wave files by changing pitch.



#### **Updating the KWS Example with Your Keywords**

To simplify this example, we assume that the name of the dataset, data loader, and model have not changed. Otherwise, the training, quantization, evaluation, and synthesis scripts and YAML file need to be updated with the new names as described in the previous section (Modifying Cats vs Dogs).



### 1. Dataset

Create a folder for the new keyword label (e.g. **'hello'**) in **ai8x-training/data/KWS/raw** and copy all samples inside that folder.

Remove **ai8x-training/data/KWS/processed** if it exists so that the data loader recreates the dataset with additional labels.

Make sure the directory structure of the datasets is correct:

```text
data -- KWS
           |--raw: contains folders with labels name, each containing 1sec .wav file for that label
```



### 2. Updating the Data Loader

Update **ai8x-training/datasets/kws20.py**:

- Add the new label (e.g. **'hello'**) to the ***sorted class dictionary in the correct location (must be sorted)*** with unique incremental values:

```python
 class_dict = {'backward': 0, 'bed': 1, 'bird': 2, 'cat': 3, 'dog': 4, 'down': 5,
         'eight': 6, 'five': 7, 'follow': 8, 'forward': 9, 'four': 10, 'go': 11,  # hello is added to 14th location
         'happy': 12, 'hello:13, 'house': 14, 'learn': 15  ....
```



- Add the new class to **KWS_get_datasets** function to the list of desired keywords (this list does not need to be sorted). All other labels will be fused in an 'unknown' class:

```python
if num_classes == 6:
      classes = ['up', 'down', 'left', 'right', 'stop', 'go']
elif num_classes == 20:
      classes = ['up', 'down', 'left', 'right', 'stop', 'go', 'yes', 'no', 'on', 'off', 'one',
         'two', 'three', 'four', 'five', 'six', 'seven', 'eight', 'nine', 'zero']
elif num_classes == 21: # additional keyword 'hello'
      classes = ['up', 'down', 'left', 'right', 'stop', 'go', 'yes', 'no', 'on', 'off', 'one',
         'two', 'three', 'four', 'five', 'six', 'seven', 'eight', 'nine', 'zero', 'hello'] # 'hello' is class 20, unknown is 21
```



- Update **KWS_20_get_datasets** function to return new number of classes. You may rename the function for clarity if needed:

```python
return KWS_get_datasets(data, load_train, load_test, num_classes=21) # 21 instead of 20
```



- Update the 'output' enumeration of the relevant definition in datasets. Add additional items in 'weight' to match the same number as 'output'. Note that the last class id will represent the 'unknown' category (e.g. 22 outputs for 21 keywords):

```python
 {
    'name': 'KWS_20',  # 20 keywords
    'input': (128, 128),
    'output': (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21), # 20: 'hello', 21:'unknown'
    'weight': (1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0.14), # one item added
    'loader': KWS_20_get_datasets,
  },
```

*To balance the optimizer for a variable number of samples for each class, you can change the weight reversely proportional to the number of samples per label.*

*For example:   `weight[i] = (size of smallest label)/(size of label i)`*



### 3. Updating the Model

Update the default num_classes in the initialization section in **ai8x-training/models/ai85net-kws20.py**:

```python
# num_classes = n keywords + 1 unknown

def __init__(
      self,
      num_classes=22, # was 21
      num_channels=128,
      dimensions=(128, 1),
      fc_inputs=7,
      bias=False,
      **kwargs
  ):
```

Note: If you choose to change the model, or the name of the dataset or model, you may need to update the YAML file in `ai8x-synthesis/networks/kws20-v3-hwc.yaml` accordingly, as described in step 7 of the previous section (Modifying Cats vs Dogs).



### 4. Training, Quantization, Evaluation, and Synthesis

Follow the general procedure to train, quantize, evaluate, and synthesize to generate the KAT C code as described earlier. Since there is already a sample input data for KWS20, there is no need to regenerate it.

Note: Training starts with creating the processed dataset from all labeled samples and could take several hours.



### 5. Updating the kws20_demo Demo Application

- Replace **cnn.c, cnn.h, weights.h, sampledata.h** with those generated in KAT C code.
- Update main.c with your own class names, e.g.:
  - `const char keywords [NUM_OUTPUTS][10]= { "UP", "DOWN", "LEFT", "RIGHT", "STOP", "GO", "YES", "NO", "ON", "OFF", "ONE", "TWO", "THREE", "FOUR", "FIVE", "SIX", "SEVEN", "EIGHT", "NINE", "ZERO", **"HELLO",** "Unknown" }; // "HELLO" is class 20`
- Make any other changes needed to print the correct class names
- Build, flash and run the code as described above

