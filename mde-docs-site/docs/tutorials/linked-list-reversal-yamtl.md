# Linked List Reversal - YAMTL Implementation

To demonstrate the YAMTL capabilities, let's look at a simple example that reverses a linked list using YAMTL. A linked list with 2 nodes (N1 and N2) is reversed through model transformation.

**Source Model**
<figure markdown>
  ![Linked list in the source model](../assets/images/linked-list-before.png){ width="300" }
  <figcaption>Linked list BEFORE transformation</figcaption>
</figure>

**Target Model**
<figure markdown>
  ![Linked list in the target model](../assets/images/linked-list-after.png){ width="300" }
  <figcaption>Linked list AFTER transformation</figcaption>
</figure>
<br/><br/>

## YAMTL Transformation

## Development Platforms

[**Download**](../assets/downloads/yamtl-linkedlistreversal.zip) the linked list reversal project (ZIP file). You can unzip and import the project into an IDE of your choice. This documentation will provide details on how to setup the example project on Eclipse, IntelliJ and VSCode.

YAMTL uses groovy scripts to define the models and transformations so generally any IDE will need some Groovy support through extensions/plug-ins. Make sure to do the necessary steps found in the [YAMTL Workspace Configuration](../yamtl.md#workspace-configuration) section before you run the project.

### **Eclipse**

Unzip the downloaded project. In Eclipse, click on ``File → Import → Existing Projects into Workspace → Select root directory → Finish``. This will import and load the project in the IDE.

Right click on ``src/test/groovy/linkedListReversal/ReverseLinkedListTest.groovy`` then ``Run as → JUnit Test``. Once the test is completed, a new ``outputList.xmi`` will be generated in the ``model`` directory. Examine this file and notice if the linked list has been reversed.

### **IntelliJ**

Within IntelliJ, go to ``File → Open`` and open the project.

Right click on ``src/test/groovy/linkedListReversal`` folder then 'Run Tests...'. 

Alternatively, click on the green run button in the top bar to run ``Tests in 'yamtl-linkedlistreversal'`` which builds the project and generates an ``outputList.xmi`` in the ``model`` directory. 

Examine this file and notice if the linked list has been reversed.

![Run gradle tests in IntelliJ](../assets/images/run-tests-intellij.gif)

### **VSCode**

In VSCode, import the project by doing ``File → Open``.

After you have imported the example project into the workspace, click on the Gradle icon in the left sidebar. Then perform ``Tasks →  build → clean``. Do ``Tasks → build → build`` to build the entire Gradle project (it also runs the ``ReverseLinkedListTest.groovy`` file). 

![Run Gradle clean and build in VSCode](../assets/images/gradle-build-vscode.gif)

Once the project is successfully built, an ``outputList.xmi`` will be generated in the ``model`` directory. Examine this file and notice if the linked list has been reversed.
