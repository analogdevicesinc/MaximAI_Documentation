# Maxim AI Documentation

This is the home for IC, EV Kit, SDK, and AI documentation for the Maxim AI product family.

**START HERE:**

->->-> **[Getting Started with the MAX78000 Evaluation Kit](./MAX78000_Evaluation_Kit/README.md)**

->->-> **[Getting Started with the MAX78000 Feather Board](./MAX78000_Feather/README.md)**

->->-> **[Getting Started with the MAXREFDES178# Cube Camera](https://github.com/MaximIntegratedAI/refdes/blob/main/maxrefdes178_doc/README.md)**

-----
- [Maxim AI Documentation](#maxim-ai-documentation)
  - [Project Structure](#project-structure)
  - [Links to MAX78000 Documentation](#links-to-max78000-documentation)
  - [Application Notes](#application-notes)
  - [Additional MAX78000 and Machine Learning Education Resources](#additional-max78000-and-machine-learning-education-resources)
      - [Videos, Webinars, Presentations, Books and More](#videos-webinars-presentations-books-and-more)

## Project Structure

The project consists of five repositories:

1. This repo (“Documentation”)

2. The software development kit (SDK), which contains peripheral drivers and example programs ready to run on the Evaluation Kit:
   [MAX78000_SDK](https://github.com/MaximIntegratedAI/MAX78000_SDK). See [here](https://www.maximintegrated.com/en/design/technical-documents/userguides-and-manuals/7/7219.html) for the most recent SDK Installation Guide.

3. The training repo, which is used for deep learning model development and training:
   [ai8x-training](https://github.com/MaximIntegratedAI/ai8x-training)

4. The synthesis repo, which is used to convert a trained model into C code using the “izer” tool:
   [ai8x-synthesis](https://github.com/MaximIntegratedAI/ai8x-synthesis)

5. The reference design repo, which contains application code and documentation for the reference designs:
   [refdes](https://github.com/MaximIntegratedAI/refdes)



## Links to MAX78000 Documentation

* Link to IC description and datasheet: [MAX78000](https://www.maximintegrated.com/en/products/microcontrollers/MAX78000.html)
* Link to IC User Guide: [MAX78000 User Guide.pdf](https://pdfserv.maximintegrated.com/en/an/ug7456.pdf)
* Link to EV Kit description and documentation: [MAX78000 EV Kit](https://www.maximintegrated.com/en/products/microcontrollers/MAX78000EVKIT.html)
  * (Older revision) Link to Rev **A** EV Kit schematic: [MAX78000 REV A including mods.pdf](./MAX78000_Evaluation_Kit/MAX78000%20REV%20A%20including%20mods.pdf)
  * (Older revision) Link to Rev **B** EV Kit schematic: [MAX78000 REV B.pdf](./MAX78000_Evaluation_Kit/MAX78000%20REV%20B.pdf)
* Link to Feather Board description and documentation: [MAX78000 Feather Board](https://www.maximintegrated.com/en/products/microcontrollers/MAX78000FTHR.html)
* Link to SDK repository: [MAX78000_SDK](https://github.com/MaximIntegratedAI/MAX78000_SDK)
* Link to SDK documentation: [SDK Docs - click on index.html](https://github.com/MaximIntegratedAI/MAX78000_SDK/blob/master/Libraries/PeriphDrivers/Documentation/MAX78000)  **Note:** HTML files are not rendered in GitHub and it is therefore recommended to view the SDK documentation from a local copy, which is automatically installed with the SDK.  To view the SDK documentation locally, double click on the index.html file found in /Libraries/PeriphDrivers/Documentation/MAX78000
* Link to Reference Design documentation and reference design application code repository: [MAX78000 Reference Designs](https://github.com/MaximIntegratedAI/refdes)
* Quick Start Guides and Optional Features: [Guides](Guides)

## Application Notes

1. [Developing Power-optimized Applications on the MAX78000](https://www.maximintegrated.com/en/design/technical-documents/app-notes/7/7417.html):

   Abstract: Power consumption is a key factor for edge Artificial Intelligence (AI) applications where the entire system is powered by small battery cells and is expected to operate for months without recharging or replacing the batteries. The MAX78000 ultra-low power AI microcontroller is built to target such applications at the edge of the IoT. In this document, various configurations are described to enable users to develop power-optimized applications on the MAX78000, along with benchmarking examples. The power optimization methods are applied to two case study applications—Keyword Spotting with 20 keywords (KWS20) and Face Identification (FaceID), and the reported results can be used as a guideline for the user’s application.

2. [Face Identification Using MAX78000](https://www.maximintegrated.com/en/design/technical-documents/app-notes/7/7364.html):

   Abstract: The MAX78000 is an ultra-low power Convolutional Neural Network (CNN) inference engine to run Artificial Intelligence (AI) computations on tiny edges of IoT. Yet the device can execute many complex networks to achieve critical and popular applications. This document describes an approach for Face Identification (FaceID) running on the MAX78000 where the model is built with Maxim's development flow on PyTorch, trained with different open datasets and deployed on the MAX78000 evaluation board.

3. [Keywords Spotting Using the MAX78000](https://www.maximintegrated.com/en/design/technical-documents/app-notes/7/7359.html):

   Abstract: Audio assistants have become very popular with range of applications from household to automotive and industrial products and IoT. Such devices constantly listen to their surroundings and wake up on pretrained keywords to execute certain commands. Power consumption is a key factor for many of such resource constrained edge applications, where the connectivity to the cloud for processing of raw data is not feasibly. The MAX78000 is a new breed of Artificial Intelligence (AI) microcontroller built to enable neural networks to execute at ultra-low power and live at the edge of the IoT. In this document, we show case the implementation of a keyword spotting application on the MAX78000. The machine learning model is built with Maxim’s development flow on PyTorch, trained with a subset of Google’s speech command dataset with 20 keywords, and deployed on the MAX78000EVKIT.



## Additional MAX78000 and Machine Learning Education Resources

#### Videos, Webinars, Presentations, Books and More

Interested in the MAX78000, or just getting started with Machine Learning? [Here's a set of videos and books to get you going.](./learning/README.md)
