# ETL

The Epsilon Transformation Language (ETL) is used to perform model-to-model transformations with Epsilon. ETL can be used to transform a random number of input models into a random number of output models of different modelling languages and technologies in a rule-based and modular manner. 

An ETL model-to-model transformation project (see image below) requires a source model and metamodel, target metamodel, an ETL program that contains the rules and transformations between the source and target model, and a build file which loads all EMF models and runs the ETL program to generate the target model (an XMI file).
<br/><br/>

<figure markdown>
  ![ETL project structure in eclipse](assets/images/etl-project-structure.png){ width="300" }
  <figcaption>File structure of an example ETL project</figcaption>
</figure>

## Installation

To use ETL, you just need to install [Epsilon on Eclipse](epsilon.md).

## ETL example

To demonstrate the ETL capabilities, let's look at a simple example that reverses a linked list using ETL. A linked list with 2 nodes (N1 and N2) is reversed through model transformation.

**Source Model**
<figure markdown>
  ![Linked list in the source model](assets/images/linked-list-before.png){ width="300" }
  <figcaption>Linked list BEFORE transformation</figcaption>
</figure>

**Target Model**
<figure markdown>
  ![Linked list in the target model](assets/images/linked-list-after.png){ width="300" }
  <figcaption>Linked list AFTER transformation</figcaption>
</figure>
<br/><br/>

There are many ways to interact with ETL: an online [Epsilon Playground](https://eclipse.dev/epsilon/playground/?d1b7114c), using Ant (Eclipse) or using Java.

### **Online playground**

!!! example "Try ETL online"
    You can run and fiddle with an ETL transformation that transforms a linked list into its reverse version in the [online Epsilon Playground](https://eclipse.dev/epsilon/playground/?d1b7114c).
<br/><br/>

### **Apache Ant (Eclipse)**

[Click here](assets/downloads/playground-example.zip) to download the linked list example project or head over to the [online Epsilon Playground](https://eclipse.dev/epsilon/playground/?d1b7114c) and select ```Download → Ant (Eclipse)``` as shown below

![Ant Download](assets/images/ant-download.gif)

Next step is to import the project in Eclipse. Once the ZIP file is downloaded, open your Eclipse IDE and do ```File → Import → Existing Projects into Workspace → Select archive file → Finish```. 

Then, right click on ``build.xml`` and choose ``Run as → Ant Build`` to build the ETL project and generate the target model. Examine the generated model (target.xmi) to discover the linked list has been reversed! 

![Ant Walkthrough](assets/images/ant-walkthrough.gif)
<br/><br/>

### **Java (Gradle)**

[Download](assets/downloads/playground-example-java-gradle.zip) the linked list Java project.

Unzip the project and import it into an IDE of your choice (e.g. Eclipse, IntelliJ, VSCode). Make sure the IDE uses **JDK 17 or higher**.

Run ``src/main/java/org/eclipse/epsilon/examples/Example.java`` to generate ``target.xmi`` containing the target model. The target model is the reversed linked list.

To run ``Example.java`` in VSCode, make sure the Gradle extension is enabled and click on the Gradle icon in the sidebar then do ``playground-example → Tasks → application → run``.