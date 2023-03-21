# MAX78002EVKIT Quick-Start Guide

<img src="img/MAX78002_EVKIT.jpg" alt="MAX78002EVKIT" width="60%"/>

---

[TOC]

---

## Introduction

This quick-start document contains info on getting started with the MAX78002EVKIT evaluation platform. It supplements the full MAX78002EVKIT datasheet with information on software development and quick tips for getting started.

## Schematic and BOM

The schematic and BOM can be found in the [MAX78002 EVKIT web page](https://www.analog.com/en/design-center/evaluation-hardware-and-software/evaluation-boards-kits/max78002evkit.html).

## Powering and Connecting to the Evaluation Kit

**This section contains !!critical!! information regarding the power-sequencing of the MAX78002EVKIT.  Please review this section to avoid damage to the board.**

### Power Sequencing

As of 3/1/2023 critical back-powering issues were identified related to the included MAX325625PICO debugger.  If the power-up and power-down sequencing below is _not_ followed, the PICO debugger could back-power the 3V3 and 1V8 rails of the MAX78002EVKIT.  This could result in permanent damage of the MAX78002 and any on-board peripherals.  To prevent this issue, follow the _exact_ sequencing below when powering the board on or off:

#### Power-**Up** Sequencing

1. Connect the [Power Supply](#power-supply) and power on the board.
2. Connect any [Serial Ports](#connecting-serial-ports).
3. Connect any [Debug Adapters](#connecting-debug-adapters)

#### Power-**Down** Sequencing

1. Disconnect any [Debug Adapters](#connecting-debug-adapters)
2. Disconnect any [Serial Ports](#connecting-serial-ports)
3. Switch off the [Power Supply](#power-supply)

### Power Supply

The MAX78002EVKIT requires an external 5V supply power supply. A 5V wall adapter is supplied in the box. Connect the 5V wall adapter to the “5V IN” barrel jack connector (J1).


<img src="img/J1.jpg" alt="J1" width="550"/>

SW1 (labeled “PWR”) is the main power switch. With 5VIN supplied, flip the switch to power the rest of the board. The “5V” status LED (D2), “1V1 CNN” (D4), and “3V3” (D3) status LEDs will turn on and can be used to verify the board is powered on successfully.

<img src="img/Power_LEDs.jpg" alt="Power LEDs" width="550"/>

### Connecting Debug Adapters

The MAX78002 is a dual-core microcontroller, with both an Arm Cortex M4 core and a RISC-V core. As a result, the EVKIT comes with two debug adapters:

A MAX32625PICO:

* For use with the Arm Cortex M4 core
* Connects to the “SWD” debug header (JH8)

<img src="img/pico_swd_connected.jpg" alt="PICO SWD Connected" width="550"/>

An Olimex [ARM-USB-OCD-H](https://www.olimex.com/Products/ARM/JTAG/ARM-USB-OCD-H/) + [ARM-JTAG 20-10 adapter](https://www.olimex.com/Products/ARM/JTAG/ARM-JTAG-20-10/):

* For use with the RISC-V core
* Connects to the “RV JTAG” debug header (JH5)
* This RISC-V debug adapter needs to be connected only for debugging the RISC-V core, but not for loading in general. Many of the examples in the MSDK are Arm-only.
* RISC-V debugging does, in general, also require simultaneous debugging of the Arm M4 core.

<img src="img/Olimex.jpg" alt="Olimex Debugger Connected" width="550"/>

### Connecting Serial Ports

The MAX78002EVKIT exposes 3 serial ports via micro-USB connectors.

#### “USB/UART” (CN2)

This serial port routes UART0 and UART1 from the MAX78002 to an [FT231XS](https://ftdichip.com/products/ft231xs/) UART to USB adapter. This is the default serial port used by most examples.

To communicate over this port, connect a micro-USB cable from the host PC to CN2. Most Operating Systems will auto-install the required drivers or already include them on the system, and the FT231XS chip will present itself as a Virtual COM Port (VCP).

If the port does _not_ present itself, then manual driver installation is required. See [this](https://ftdichip.com/drivers/vcp-drivers/) page from the official FTDI site.

ADI’s examples use the following serial communication settings by default:

* BAUD Rate: 115200
* Data: 8-bit
* Parity: None
* Stop bits: 1
* Flow control: None

Serial communication settings can be modified in the `MaximSDK/Libraries/Boards/MAX78002/EvKit_V1/Include/board.h` file. Jumper settings should be checked when modifying the default settings, such as changing from UART0 to UART1. Refer to the [MAX78002 EVKIT Datasheet](ihttps://www.analog.com/en/design-center/evaluation-hardware-and-software/evaluation-boards-kits/max78002evkit.html) for more details on jumper settings.

#### “USB/AI” (CN3)

This serial port connects directly to the USB 2.0 Hi-Speed Controller peripheral on the MAX78002. The serial port will not be functional unless the host firmware running on the MAX78002 sets it up.

#### “USB/PWR MON” (CN1)

This serial port connects to a secondary MAX32625 microcontroller that manages the power monitoring sub-circuit of the MAX78002EVKIT. This port will present itself as a standard USB Serial Device.

### Connecting Camera Modules

#### Pcam 5C

The MAX78002EVKIT includes a Pcam 5C CSI camera module, which should be connected to the CSI connector J8 for evaluation.

1. Connect the CSI ribbon cable to the Pcam 5C.

    Looking at the bottom of the Pcam 5C board, the blue strip of the ribbon cable should be facing you.

    <img src="img/pcam_bottom.jpg" alt="Pcam 5C Bottom" width="550"/>

2. Connect the CSI ribbon cable to the CSI connector J8 on the MAX78002EVKIT.

    Slide out the thin darker part of the connector to open it, then insert the ribbon cable.

    The blue strip of the ribbon cable should be facing up.

    Snap the thin darker part of the connector to close it and lock the cable in place.

    <img src="img/csi_connector.jpg" alt="CSI Connector" width="550"/>

3. Remove the cap from the CSI camera module before using it.

    <img src="img/pcam_top.jpg" alt="Pcam Top" width="550"/>

#### OVM7692

The MAX78002EVKIT also comes with an OVM7692 DVP (Digital Video Port) camera module, which should be connected to the DVP connector JH7 for evaluation. This camera also requires an external adapter board which is included with the MAX78002EVKIT.

1. Insert the OVM7692 camera module into JH2 on the DVP adapter board.

    The camera module should be facing toward the outer edge of the adapter board.

    <img src="img/DVP_1.jpg" alt="DVP Camera Module Face" width="550"/>

    <img src="img/DVP_2.jpg" alt="DVP Camera Module Top" width="550"/>

2. Remove the small amber cover sticker from the OVM7692 camera module to expose the lens.

    <img src="img/OVM7692.jpg" alt="DVP Camera Module Cover Removed" width="550"/>

3. Remove the plastic cover from the DVP port of the adapter board and attach one side of the included DVP cable.

    Use the end of the cable with the notch such that the cable comes out from the adapter board.

    <img src="img/DVP_3.jpg" alt="DVP Cable on Adapter" width="550"/>

4. Attach the other side of the DVP cable to JH7 on the MAX78002EVKIT.

    **It should be noted that removing the cable after fully inserting it into JH7 can very easily damage it.** To safely remove the DVP cable use a small flat-head to push out the plastic notch. **Do no remove the cable by pulling on the cable wire itself.**

    <img src="img/DVP_4.jpg" alt="DVP Cable on EVKIT" width="550"/>

## First-time Firmware Updates

The firmware for the MAX32625PICO (PICO) debug adapter should be updated before using the MAX78002EVKIT. These updates contain bug fixes and improvements that are required to ensure proper operation of the kit.

### Updating the MAX32625PICO (PICO) Debug Adapter Firmware

1. Download the [`max32625_max78000fthr_if_crc_v1.0.2.bin`](https://github.com/MaximIntegratedAI/MaximAI_Documentation/raw/master/MAX78002_Evaluation_Kit/MAX32625PICO_files/max32625_max78000fthr_if_crc_v1.0.2.bin) file.

    If you have cloned this repository, the file can also be found in the `MAX78002_Evaluation_Kit/MAX32625PICO_files` folder.

2. Connect the included micro-USB cable to the PICO _without_ connecting the other side of the cable to your host PC yet.

    <img src="img/pico_partial_connected.jpg" alt="PICO Partially Connected" width="400"/>

3. Press and hold the pushbutton on the top of the PICO.

    <img src="img/pico_pushbutton.jpg" alt="PICO Pushbutton" width="400"/>

4. _While holding down the pushbutton on the PICO_ connect the other side of the micro-USB cable to your host PC.

    Keep the pushbutton held down until the LED on the PICO blinks and becomes solid.

    <img src="img/pico_connected.jpg" alt="PICO Connected" width="400"/>

5. A `MAINTENANCE` drive should now appear on your file system.

    <img src="img/MAINTENANCE.jpg" alt="Maintenance Drive Image" width="400"/>

6. Drag and drop the [`max32625_max78000fthr_if_crc_v1.0.2.bin`](https://github.com/MaximIntegratedAI/MaximAI_Documentation/raw/master/MAX78002_Evaluation_Kit/MAX32625PICO_files/max32625_max78000fthr_if_crc_v1.0.2.bin) file onto the `MAINTENANCE` drive. This will flash the PICO with the updated firmware.

    <img src="img/MAINTENANCE.jpg" alt="Maintenance Drive Image" width="400"/>

    <img src="img/drag_and_drop.JPG" alt="Drag and Drop" width="400"/>

    <img src="img/pico_flashing.JPG" alt="Flashing" width="400"/>

7. Once the flashing is complete, the PICO will restart and present itself as a `DAPLINK` drive.

    <img src="img/DAPLINK.jpg" alt="Daplink Drive" width="400"/>

8. Open the `DAPLINK` drive.

    <img src="img/DAPLINK_opened.jpg" alt="Daplink Opened" width="400"/>

9. Open the `DETAILS.TXT` file and verify the contents match the [`MAX32625PICO_files/DETAILS.TXT`](https://github.com/MaximIntegratedAI/MaximAI_Documentation/raw/master/MAX78002_Evaluation_Kit/MAX32625PICO_files/DETAILS.TXT) file in this repository. Specifically - the “Git SHA” field should match exactly.

    <img src="img/DETAILS_Git_SHA.jpg" alt="Details GIT SHA" width="400"/>

10. Your PICO debugger is now ready to use with the MAX78002EVKIT.

## First-time Operation

The MAX78002EVKIT will come pre-flashed with a Keyword-spotting demo program out of the box. This demo showcases the “kws20” Neural Network, which has been trained on Google’s speech commands dataset to recognize the following 20 keywords:

 up, down, left, right, stop, go, yes, no, on, off, one, two, three, four, five, six, seven, eight, nine, zero

To exercise the demo power on the EVKIT and follow the instructions on the TFT display.

Optionally, connect to the USB/UART (CN2) [Serial port](#connecting-serial-ports) to monitor the serial output of the demo program.

The source code for the demo can be found in the `Examples/MAX78002/CNN/kws20_demo` folder of the MSDK.

## Software Development

### Setup

Embedded development for the MAX78002EVKIT is enabled by the Maxim Microcontroller SDK (“MSDK”), now a part of Analog Devices.

See the [**MSDK User Guide**](https://analog-devices-msdk.github.io/msdk/USERGUIDE/) for detailed documentation on installation, setup, and getting started.

### Debugger Limitations of the MAX78002

Before continuing into the development quick-starts below, it’s important to note some limitations when debugging the MAX78002:

* The debugger can not be connected to the MAX78002 _while_ the device is in reset.
* The MAX78002 can not be debugged while the device is in Sleep, Low Power Mode, Micro Power Mode, Standby, Backup, or Shutdown mode.

    These modes shut down the SWD clock.

    Examples that enter these modes have a 2-second delay in place at the beginning of code execution to give the debugger a window to connect. For low-power development, it is also recommended to have the same delay in place.

These limitations may make the device difficult (or impossible) for the debugger to connect in certain states. In such cases, the device can be recovered from the “locked out” firmware by erasing the application-space in the flash memory bank. See [How to Unlock a MAX78002 That Can No Longer Be Programmed](#how-to-unlock-a-max78002-that-can-no-longer-be-programmed).

### How to Unlock a MAX78002 That Can No Longer Be Programmed

Some [debugger limitations](#debugger-limitations-of-the-max78002) may make the device difficult to connect to. In such cases, the device can be recovered from the “locked out” firmware by erasing the application-space in the flash memory bank.

Before following the procedure below, ensure that you have updated the PICO debugger firmware to the latest version. See [Updating the MAX32625PICO (PICO) Debug Adapter Firmware](#updating-the-max32625pico-pico-debug-adapter-firmware)

1. Connect the PICO debugger to the MAX78002 EVKIT SWD debugger port. See [Connecting Debug Adapters](#connecting-debug-adapters) for more detailed instructions on this step.

2. Connect the PICO debugger to the host PC with the included micro-USB cable.

3. Open the DAPLINK` drive on the host PC.

    <img src="img/DAPLINK.jpg" alt="DAPLINK Drive" width="550"/>

4. Open the `DETAILS.TXT` inside of the `DAPLINK` drive in a text editor.

    <img src="img/DAPLINK_opened.jpg" alt="DETAILS.TXT" width="550"/>

5. Verify that the “Automation allowed” field is set to 1.

    <img src="img/DETAILS_automation_allowed.jpg" alt="Automation Allowed" width="550"/>

    If this field is _not_ set to 1, follow the procedure below:

    1. Create a new _empty_ file, and save it as `auto_on.cfg`.

    2. Press and hold the pushbutton on top of the PICO.

        <img src="img/pico_pushbutton.jpg" alt="PICO Pushbutton" width="550"/>

    3. _While holding the pushbutton_, drag and drop the `auto_on.cfg` file onto the `DAPLINK` drive.

        <img src="img/auto_on.cfg.jpg" alt="auto_on.cfg" width="550"/>

    4. Continue holding the pushbutton until the file is finished transferring over, then release it.

    5. The PICO should power cycle, and the DAPLINK drive should re-appear with “Automation allowed” set to 1.

6. Power on the MAX78002EVKIT (if it isn’t already).

7. Drag and drop the [`MAX32625PICO_files/erase.act`](https://github.com/MaximIntegratedAI/MaximAI_Documentation/raw/master/MAX78002_Evaluation_Kit/MAX32625PICO_files/erase.act) file onto the `DAPLINK` drive.

    <img src="img/erase.act.jpg" alt="Erase.act" width="550"/>

8. The PICO debugger will mass erase the MAX78002’s application-space flash bank, which will completely wipe any application firmware that is programmed on the device.

9. Power cycle the MAX78002EVKIT. The MAX78002 is now blank and ready to re-program. The debugger should have no issues connecting to the device in this blank state.

Note:  In order to avoid the locked out state to begin with, it is recommended that during code development, a delay be placed at the beginning of user code in order to give the debug adapter an opportunity to communicate with or halt the processor. A delay of 2 seconds is ideal so that the debugger can be attached manually.

## Machine Learning (ML) Development

As the MAX78002 contains a powerful Convolution Neural Network (CNN) accelerator, there is also the Machine Learning side of development. This is done with a separate set of tools. The example projects that are found in the `CNN` sub-folder of the MAX78002 examples have been created with these tools. More specifically, they have been created with the `ai8x-synthesis` (“izer”) tool, which converts a trained model into C code that can be compiled and flashed onto the MAX78002.

### ML Overview

The documentation associated with the setup and usage of these tools is significant. Here are the links to get started:

* [Analog Devices AI Github Repository](https://github.com/maximintegratedAI)
* [ai8x-synthesis tool](https://github.com/MaximIntegratedAI/ai8x-synthesis)
* [ai8x-training tool](https://github.com/MaximIntegratedAI/ai8x-training)
* [Workflow Guide](https://github.com/MaximIntegratedAI/MaximAI_Documentation/blob/master/Guides/MAX78000_Workflow_Guide.md#max78000-workflow-guide)

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

The setup and usage of the machine learning tools is thoroughly documented in the [README.md](https://github.com/MaximIntegratedAI/ai8x-synthesis/blob/develop/README.md) file that can be found in the root directory of both the “izer” and training tools. See the [Installation](https://github.com/MaximIntegratedAI/ai8x-training/blob/develop/README.md#installation) section for detailed instructions.

### ML Usage

Detailed usage of the “izer” and training tools is beyond the scope of this document. The [Workflow Guide](https://github.com/MaximIntegratedAI/MaximAI_Documentation/blob/master/Guides/MAX78000_Workflow_Guide.md) is a great introduction, and the [Videos](https://www.analog.com/en/education/education-library/videos/6313215159112.html) dive deeper.

Additionally, the [README](https://github.com/MaximIntegratedAI/ai8x-synthesis/blob/develop/README.md) contains all of the usage information for the tools.

Below are a few exercises to get started after setup is complete:

* Run the `gen-demos-max78002.sh` script found in the root directory of the “izer” tool and reference its [Command Line Arguments](https://github.com/MaximIntegratedAI/ai8x-synthesis#command-line-arguments-3) table to see how it works.

* Build and flash the output of one of the `gen-demos-max78002.sh` projects and verify that the generated “known answer” test passes.

* Locate the YAML files for the pre-trained models in the “izer” tool and reference the [YAML Network Description](https://github.com/MaximIntegratedAI/ai8x-synthesis#yaml-network-description) to see how they work.

* Run `train_all_models.sh` from the training repository and reference its [Command Line Arguments](https://github.com/MaximIntegratedAI/ai8x-training#command-line-arguments) to see how it works.

* Familiarize yourself with the concept of data loaders with [Application Note 7600](https://www.maximintegrated.com/en/design/technical-documents/app-notes/7/7600.html) and explore the pre-written data loaders found in the [datasets](https://github.com/MaximIntegratedAI/ai8x-training/tree/develop/datasets) directory of the training tool.

## Power Monitor Sub-Circuit

The MAX78002EVKIT includes a dedicated power-monitoring sub-circuit (PMON) that allows the user to measure the power consumption of the MAX78002 to determine the active and idle power, and the energy and time to perform CNN inferences as well as kernel and data loading. This separate sub-circuit can be found in the bottom right corner of the board and has its own TFT display, USB virtual serial port, and microcontroller.

<img src="img/PMON.jpg" alt="Power Monitor Sub-circuit" width="550"/>

The PMON firmware version can be checked by repeatedly pressing either the PWR MODE SEL LEFT or RIGHT button to cycle through the screens to view the information page.

<img src="img/show_info_page_78002.png" alt="Power Monitor Firmware Version" style="zoom:125%;"/>

At the time of this writing, latest version of the power monitor sub-circuit’s firmware is v2.0. Check the reported version against the latest MAX78002EVKIT PMON firmware available [HERE](https://github.com/MaximIntegratedAI/MaximAI_Documentation/tree/master/MAX78002_Evaluation_Kit/PMON_Firmware) 

Detailed usage information on the PMON operation, including measurements, how to instrument code and updating the firmware is available in the [MAX7800x Power Monitor and Energy Benchmarking Guide](https://github.com/MaximIntegratedAI/MaximAI_Documentation/blob/master/Guides/MAX7800x%20Power%20Monitor%20and%20Energy%20Benchmarking%20Guide.md).
