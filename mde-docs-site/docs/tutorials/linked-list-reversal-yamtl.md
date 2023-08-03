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

There are 2 aspects of the linked list which are changed from the source model to the target model (thus requiring two tranformation rules): 

1. The head of the linked list is swapped with its tail. 
2. The nodes are reversed i.e. pointing to the previous node instead of the next one.

### YAMTL Definition

The YAMTL definition is in a Groovy script as follows: 

``` linenums="1"
class ReverseLinkedList extends YAMTLModule {
	public ReverseLinkedList(EPackage llPk) {
		YAMTLGroovyExtensions_dynamicEMF.init(this)
		header().in('in', llPk).out('out', llPk)
		
		ruleStore([
			rule('LinkedList2LinkedList')
				.in('s', llPk.LinkedList)
				.out('t', llPk.LinkedList, {
					t.nodes = fetch(s.nodes) //Mapping
					t.head = fetch(allInstances(llPk.Node).find{it.next==null})
				}),
			
			rule('Node2Node')
				.in('s', llPk.Node)
				.out('t', llPk.Node, {
					t.name = s.name
					t.next = fetch(allInstances(llPk.Node).find{it.next==s})
				})
		])
	}
}
```

Let's understand each line of code to learn the YAMTL syntax and semantics.

In Line 1, the class (also name of the file) extends ``YAMTLModule`` (which is imported) to highlight the code block containing YAMTL definition.<br>
In Line 2, the public method has an argument ``llPk`` (referring to linked list package) of the type ``EPackage``. This adds EMF extensions to interpret getters/setters of an EObject and reference to classifiers inside an EPackage. Now, the classes in the ``.emf`` file can be accessed.<br>
In Line 3, the YAMTL Groovy Extensions for EMF import is initialised.<br>
In Line 4, the in and out parameters (of the source and target models respectively) for the model transformation is defined.<br>
In Line 6, the list of transformation rules is initialised using ``ruleStore`` operation. Each rule is contained here, separated by commas (,).<br>
In Line 7-9, the first rule ``LinkedList2LinkedList`` is created with the input source element 's' of the ``Node`` type accessed from the ``llPk`` package. The corresponding target element 't' is of the data type ``Node`` and the ``out`` operation also contains execution statements written in curly braces ```{}```.<br>
In Line 10, nodes of the ``LinkedList`` 's' are all fetched and assigned to the target ``LinkedList`` element 't'. Here, the references or mappings of the nodes are passed to the target element.<br>
In Line 11, all ``llPk.node``s are queried to find a ``Node`` object that does not reference or point to another ``Node`` object i.e. it points to ``null``. The result of the ``fetch`` operation is assigned as the `head` attribute of the target element. Remember, this is one of the aspects discussed above, the head of the linked list is swapped with its tail.<br>
In Line 14-16, a new rule ``Node2Node`` is defined, its source element 's' and target element 't' have been initialised. Both elements are of the same type ``Node`` accessed via the ``llPk`` package.<br>
In Line 17, the source element's ``name`` attribute is assigned to the target element's ``name`` field.<br>
In Line 18, all ``Node`` objects in the source model are queried to find a ``Node`` object that has a ``name`` attribute which points/references to the source element 's'. The resultant ``Node`` object is fetched and assigned to the target element's ``next`` attribute. Recall, the second transformation aspect previously discussed, each node points to the previous node instead of the next one.<br>

### Transformation Test Script

Besides the actual transformation definition, you will also need a Groovy test script that automates the entire transformation process. Here, the relevant project files are configured and test assertions are implemented. Check out the test script for the linked list reversal example below:

```
class ReverseLinkedListTest extends YAMTLModule {
	final BASE_PATH = 'model'
	
	@Test
	def void testLinkedList() {
		// model transformation execution example
		def metamodel = YAMTLModule.loadMetamodel(BASE_PATH + '/LinkedList.ecore') as EPackage
		def xform = new ReverseLinkedList(metamodel)
		xform.loadInputModels(['in': BASE_PATH + '/inputList.xmi'])
		xform.execute()
		xform.saveOutputModels(['out': BASE_PATH + '/outputList.xmi'])

		// test assertion
		def actualModel = xform.getOutputModel('out')
		EMFComparator comparator = new EMFComparator();
		def expectedResource = xform.loadModel(BASE_PATH + '/expectedOutput.xmi', false)
		assertTrue( comparator.equals(expectedResource.getContents(), actualModel.getContents()) );
		
	}

}
```

The transformation is executed through a Gradle build run which also runs this test script. In the code snippet above, ``testLinkedList()`` method contains the code for model transformation execution and tests assertion. First, the ``metamodel`` is loaded from a ``.ecore`` file as an ``EPackage``. Then, ``xform`` initialises a new ``ReverseLinkedList`` transformation definition and passes the metamodel as an ``EPackage`` (``llPk`` was a reference to the metamodel). The input parameter within the YAMTL definition script is set to be the relative location to the ``inputList.xmi`` file which contains the source model. This source model is loaded into the model transformation, the transformation rules are executed and the output model returned from ``ReverseLinkedList()`` is saved at a defined location through the output parameter. After the transformation is completed, some tests can be run to check the contents of the generated target model. The output model is compared against an expected output ``expectedOutput.xmi`` using ``EMFComparator``. An assertion is set to ensure that the contents of the actual model exactly match the contents of the expected model.


### Source and Target Metamodel

```
<?xml version="1.0" encoding="UTF-8"?>
<ecore:EPackage xmi:version="2.0" xmlns:xmi="http://www.omg.org/XMI"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:ecore="http://www.eclipse.org/emf/2002/Ecore" name="linkedlist"
	nsURI="linkedlist" nsPrefix="">
	<eClassifiers xsi:type="ecore:EClass" name="LinkedList">
		<eStructuralFeatures xsi:type="ecore:EReference" name="head"
			eType="#//Node" />
		<eStructuralFeatures xsi:type="ecore:EReference" name="nodes"
			upperBound="-1"
			eType="#//Node" containment="true" />
	</eClassifiers>
	<eClassifiers xsi:type="ecore:EClass" name="Node">
		<eStructuralFeatures xsi:type="ecore:EAttribute" name="name"
			eType="ecore:EDataType http://www.eclipse.org/emf/2002/Ecore#//EString" />
		<eStructuralFeatures xsi:type="ecore:EReference" name="next"
			eType="#//Node" />
	</eClassifiers>
</ecore:EPackage>
```

### Source Model

```
<?xml version="1.0" encoding="UTF-8"?>
<LinkedList
	xmi:version="2.0"
	xmlns:xmi="http://www.omg.org/XMI"
	xmlns="linkedlist"
	head="//@nodes.0">
	<nodes name="N1"
		next="//@nodes.1" />
	<nodes name="N2" />
</LinkedList>
```

### Target Model

```
<?xml version="1.0" encoding="ISO-8859-1"?>
<LinkedList
  xmi:version="2.0"
  xmlns:xmi="http://www.omg.org/XMI"
  xmlns="linkedlist"
  head="//@nodes.1">
  <nodes name="N1" />
  <nodes name="N2" next="//@nodes.0" />
</LinkedList>
```

## Development Platforms

[**Download**](../assets/downloads/yamtl-linkedlistreversal.zip) the linked list reversal project (ZIP file). You can unzip and import the project into an IDE of your choice. This documentation will provide details on how to setup the example project on Eclipse, IntelliJ and VSCode.

YAMTL uses groovy scripts to define the models and transformations so generally any IDE will need some Groovy support through extensions/plug-ins. Make sure to have YAMTL correctly configured or do the necessary steps found in the [YAMTL Workspace Configuration](../yamtl.md#workspace-configuration) section before you run the project.

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

## References

* [YAMTL Syntax](https://dl.acm.org/doi/10.1145/3239372.3239386)
* [YAMTL Support](https://link.springer.com/article/10.1007/s10009-020-00583-y)
* [YAMTL Homepage](https://arturboronat.github.io/yamtl/)
