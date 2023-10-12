# Analog Devices AI Documentation

This is the home for IC, EV Kit, MSDK, and AI documentation for the Analog Devices MAX78000/MAX78002 AI product family.

**START HERE:**

->->-> **[Getting Started with the MAX78000 Evaluation Kit](./MAX78000_Evaluation_Kit/README.md)**

->->-> **[Getting Started with the MAX78000 Feather Board](./MAX78000_Feather/README.md)**

->->-> **[Getting Started with the MAX78002 Evaluation Kit](./MAX78002_Evaluation_Kit/README.md)**

->->-> **[Getting Started with the MAXREFDES178# Cube Camera](https://github.com/Analog-Devices-MSDK/refdes/blob/main/maxrefdes178_doc/README.md)**

->->-> **Understanding Artificial Intelligence Video Series**
- [Episode 1 - Who Needs Machine Learning Anyways?](https://www.analog.com/en/education/education-library/videos/6313215159112.html)
- [Episode 2 - Thinking About Machine Learning](https://www.analog.com/en/education/education-library/videos/6313214510112.html)
- [Episode 3 - All About Models](https://www.analog.com/en/education/education-library/videos/6313216827112.html)
- [Episode 4 - All About Training](https://www.analog.com/en/education/education-library/videos/6313215699112.html)
- [Episode 5 - Say Hello to the MAX78000](https://www.analog.com/en/education/education-library/videos/6313217809112.html)
- [Episode 6 - From Checkpoint to C](https://www.analog.com/en/education/education-library/videos/6313215449112.html)
- [Episode 7 - How to Train a Network](https://www.analog.com/en/education/education-library/videos/6313213231112.html)
- [Episode 8 - The MAX78000FTHR](https://www.analog.com/en/education/education-library/videos/6313213346112.html)
- [Episode 8.5 - Visual Studio Code](https://www.analog.com/en/education/education-library/videos/6313212752112.html)

-----

- Analog Devices AI Documentation
  - [Project Structure](#project-structure)
  - [Links to MAX78000/MAX78002 Documentation](#links-to-max78000max78002-documentation)
  - [Application Notes](#application-notes)
  - [Additional MAX78000/MAX78002 and Machine Learning Education Resources](#additional-max78000max78002-and-machine-learning-education-resources)
    - [Videos, Webinars, Presentations, Books and More](#videos-webinars-presentations-books-and-more)

## Project Structure

The project consists of five repositories:

1. This repo (“Documentation”)

2. The microcontroller software development kit (MSDK), which contains peripheral drivers and example programs ready to run on the Evaluation Kit:
   [Analog Devices MSDK](https://github.com/Analog-Devices-MSDK/msdk) (includes support for both MAX78000 and MAX78002). See [here](https://www.analog.com/media/en/technical-documentation/user-guides/maxim-micro-sdk-maximsdk-installation-and-maintenance-user-guide.pdf) for the most recent MSDK Installation Guide.

3. The training repo, which is used for deep learning model development and training:
   [ai8x-training](https://github.com/MaximIntegratedAI/ai8x-training)

4. The synthesis repo, which is used to convert a trained model into C code using the “izer” tool:
   [ai8x-synthesis](https://github.com/MaximIntegratedAI/ai8x-synthesis)

5. The reference design repo, which contains application code and documentation for the reference designs:
   [refdes](https://github.com/Analog-Devices-MSDK/refdes)



## Links to MAX78000/MAX78002 Documentation

- MAX78000
  - Link to IC description and datasheet: [MAX78000](https://www.analog.com/en/products/max78000.html)
  - Link to IC User Guide: [MAX78000 User Guide.pdf](https://www.analog.com/media/en/technical-documentation/user-guides/max78000-user-guide.pdf)
  - Link to MAX78000 EV Kit description and documentation: [MAX78000 EV Kit](https://www.analog.com/en/design-center/evaluation-hardware-and-software/evaluation-boards-kits/max78000evkit.html)
    - (Older revision) Link to Rev **A** EV Kit schematic: [MAX78000 REV A including mods.pdf](./MAX78000_Evaluation_Kit/MAX78000%20REV%20A%20including%20mods.pdf)
    - (Older revision) Link to Rev **B** EV Kit schematic: [MAX78000 REV B.pdf](./MAX78000_Evaluation_Kit/MAX78000%20REV%20B.pdf)

  - Link to MAX78000 Feather Board description and documentation: [MAX78000 Feather Board](https://www.analog.com/en/design-center/evaluation-hardware-and-software/evaluation-boards-kits/max78000fthr.html)
  - Link to MAX78000 Reference Design documentation and reference design application code repository: [MAX78000 Reference Designs](https://github.com/Analog-Devices-MSDK/refdes)

- MAX78002
  - Link to IC description and datasheet: [MAX78002](https://www.analog.com/en/products/max78002.html)
  - Link to IC User Guide: [MAX78002 User Guide (Preliminary).pdf](./MAX78002/MAX78002%20User%20Guide%20Preliminary.pdf)
  - Link to MAX78002 EV Kit description and documentation: [MAX78002 EV Kit](https://www.analog.com/en/design-center/evaluation-hardware-and-software/evaluation-boards-kits/max78002evkit.html)
- MAX78000 and MAX78002
  - Link to MSDK repository (for both MAX78000 and MAX78002): [Analog Devices MSDK](https://github.com/Analog-Devices-MSDK/msdk)
  - Link to MSDK documentation: [MSDK Docs - click on index.html](https://github.com/Analog-Devices-MSDK/msdk/blob/master/Documentation/)  **Note:** HTML files are not rendered in GitHub and it is therefore recommended to view the MSDK documentation from a local copy, which is automatically installed with the MSDK.
  - Quick Start Guides and Optional Features: [Guides](Guides)


## Application Notes

1. [Developing Power-optimized Applications on the MAX78000](https://www.analog.com/en/app-notes/developing-power-optimized-apps-on-the-max78000.html):

   *Abstract:* Power consumption is a key factor for edge Artificial Intelligence (AI) applications where the entire system is powered by small battery cells and is expected to operate for months without recharging or replacing the batteries. The MAX78000 ultra-low power AI microcontroller is built to target such applications at the edge of the IoT. In this document, various configurations are described to enable users to develop power-optimized applications on the MAX78000, along with benchmarking examples. The power optimization methods are applied to two case study applications—Keyword Spotting with 20 keywords (KWS20) and Face Identification (FaceID), and the reported results can be used as a guideline for the user’s application.

2. [Face Identification Using MAX78000](https://www.analog.com/en/technical-articles/face-identification-using-max78000.html):

   *Abstract:* The MAX78000 is an ultra-low power Convolutional Neural Network (CNN) inference engine to run Artificial Intelligence (AI) computations on tiny edges of IoT. Yet the device can execute many complex networks to achieve critical and popular applications. This document describes an approach for Face Identification (FaceID) running on the MAX78000 where the model is built with ADI’s development flow on PyTorch, trained with different open datasets and deployed on the MAX78000 evaluation board.

3. [Keywords Spotting Using the MAX78000](https://www.analog.com/en/design-notes/keywords-spotting-using-the-max78000.html):

   *Abstract:* Audio assistants have become very popular with range of applications from household to automotive and industrial products and IoT. Such devices constantly listen to their surroundings and wake up on pretrained keywords to execute certain commands. Power consumption is a key factor for many of such resource constrained edge applications, where the connectivity to the cloud for processing of raw data is not feasibly. The MAX78000 is a new breed of Artificial Intelligence (AI) microcontroller built to enable neural networks to execute at ultra-low power and live at the edge of the IoT. In this document, we show case the implementation of a keyword spotting application on the MAX78000. The machine learning model is built with ADI’s development flow on PyTorch, trained with a subset of Google’s speech command dataset with 20 keywords, and deployed on the MAX78000EVKIT.

4. [Data Loader Design for MAX78000 Model Training](https://www.analog.com/en/app-notes/data-loader-design-for-max78000-model-training.html):

   *Abstract:* The MAX78000, Artificial Intelligence Microcontroller with Ultra-Low-Power Convolutional Neural Network Accelerator, can effectively run artificial intelligence models on the chip. Users should first develop a neural network model, using Analog Devices’s development flow on PyTorch. The MAX78000 synthesizer tool then accepts the PyTorch checkpoint and the model description in the YAML format to automatically generate the C code to be compiled and executed on the MAX78000. One of the essential software components used in the model development phase is the data loader, which is responsible for application-specific data preparation tasks. This document describes principles and design considerations on a data loader implementation when preparing application-specific training and validation/test set entities suited for the MAX78000 model training.

5. [Developing Power-Optimized Applications on MAX78002](./MAX78002/AN7664-Developing%20Power-Optimized%20Applications%20on%20MAX78002.pdf)

   *Abstract:* Power consumption is a key factor for edge AI applications, where the entire system is powered by small battery cells and is expected to operate for months without recharging or replacing the batteries. The MAX78002 ultra-low power AI microcontroller is built to target such applications at the edge of the internet-of-things (IoT). This document describes various options to develop power-optimized applications on the MAX78002 and presents benchmarking examples.




## Additional MAX78000/MAX78002 and Machine Learning Education Resources

### Videos, Webinars, Presentations, Books and More

Interested in the MAX78000/MAX78002, or just getting started with Machine Learning? [Here’s a set of videos and books to get you going.](./learning/README.md)
