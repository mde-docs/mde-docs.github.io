# Linked List Reversal - ETL Implementation

To demonstrate the ETL capabilities, let's look at a simple example that reverses a linked list using ETL. A linked list with 2 nodes (N1 and N2) is reversed through model transformation.

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

## ETL Transformation

There are 2 aspects of the linked list which are changed from the source model to the target model (thus requiring two rules): 

1. The head of the linked list is swapped with its tail. 
2. The nodes are reversed i.e. pointing to the previous node instead of the next one.


**First, the linked list's head is reversed:**

``` linenums="1"
rule LinkedList2LinkedList 
    transform s : Source!LinkedList
    to t : Target!LinkedList {
        
    t.nodes ::= s.nodes;
    t.head ::= Source!Node.all.selectOne(n|n.next == null);
}
```

Let's have a look at each line of code to understand ETL logic and semantics:

In line 1, ``rule`` declares a transformation rule with a unique name 'LinkedList2LinkedList'. <br> 
In line 2, ``transform`` keyword indicates the source parameter we want to transform in this rule. Thus, a source parameter name 's' and a source parameter type 'Source!LinkedList' (the source metamodel's LinkedList class is being referenced here). <br> 
In line 3, similar to the previous line's format, `to` keyword is followed by the target parameter name 't' and target parameter type 'Target!LinkedList'. The target element properties are being configured here. The open curly brace ({) indicates the start of the rule's ``body``.<br>
In line 5, the target element's nodes are assigned using the EOL SpecialAssignment operator (::=) to be the equivalent of the source element's nodes. Please note that ::= is the same as an ``equivalent()`` operation. <br>
In line 6, the target element's head ``t.head`` is assigned the equivalent value of a node from the source model that points to ``null`` i.e. it is the last node.<br>
In line 7, the close curly brace (}) indicates the end of the ``body`` and the dedent means the end of the transformation rule.

**Second, the nodes are reversed:**

``` linenums="1"
rule Node2Node
    transform s : Source!Node
    to t : Target!Node {
    
    t.name = s.name;
    t.next ::= Source!Node.all.selectOne(n|n.next = s);
}
```

In this rule, the source and target parameter types are also the same. Remember, the linked list is only meant to be reversed hence the structural properties of the linked list remain unchanged. In the ``transform`` statement, a source parameter 's' of the type 'Source!Node' (Node class in the source metamodel) is transformed ``to`` the target parameter 't' of the type 'Target!Node' (same Node class definition as in the source metamodel). The target element is assigned the same name as the source element. [SpecialAssignment operator](https://eclipse.dev/epsilon/doc/etl/#overriding-the-semantics-of-the-eol-special-assignment-operator) (::=) is not used here because ``name`` is a string attribute of a ``Node`` class and not a reference. The target element's ``next`` property is assigned the equivalent value of a node from the source model whos ``next`` value is the source node element 's'.

The example project also includes other important files: Source model (.xmi), Source metamodel (.emf) and a Target metamodel (.emf) which are listed below.

### Source Model

```
<?nsuri linkedlist?>
<linkedlist head="N1">
    <node label="N1" next="N2"/>
    <node label="N2"/>
</linkedlist>
```

### Source and Target Metamodel

```
@namespace(uri="linkedlist", prefix="")
package linkedlist;

class LinkedList {
	ref Node head;
	val Node[*] nodes;
}

class Node {
    attr String name;
    ref Node next;
}
```

**Note:** Usually source and target metamodels may not be the same. In this linked list reversal example, the data structure did not need to be changed but the property values.

## Development Platforms

There are many ways to interact with ETL: an online [Epsilon Playground](https://eclipse.dev/epsilon/playground/?d1b7114c), using Ant (Eclipse) or using Java. Try the linked list reversal example project using any of the three options.

### **Online playground**

!!! example "Try ETL online"
    You can run and fiddle with an ETL transformation that transforms a linked list into its reverse version in the [online Epsilon Playground](https://eclipse.dev/epsilon/playground/?d1b7114c).
<br/><br/>

### **Apache Ant (Eclipse)**

[Click here](../assets/downloads/playground-example.zip) to download the linked list example project or head over to the [online Epsilon Playground](https://eclipse.dev/epsilon/playground/?d1b7114c) and select ```Download → Ant (Eclipse)``` as shown below

![Ant Download](../assets/images/ant-download.gif)

Next step is to import the project in Eclipse. Once the ZIP file is downloaded, open your Eclipse IDE and do ```File → Import → Existing Projects into Workspace → Select archive file → Finish```. 

Then, right click on ``build.xml`` and choose ``Run as → Ant Build`` to build the ETL project and generate the target model. Examine the generated model (target.xmi) to discover the linked list has been reversed! 

![Ant Walkthrough](../assets/images/ant-walkthrough.gif)
<br/><br/>

### **Java (Gradle)**

[Download](../assets/downloads/playground-example-java-gradle.zip) the linked list Java project.

Unzip the project and import it into an IDE of your choice (e.g. Eclipse, IntelliJ, VSCode). Make sure the IDE uses **JDK 17 or higher**.

Run ``src/main/java/org/eclipse/epsilon/examples/Example.java`` to generate ``target.xmi`` containing the target model. The target model is the reversed linked list.

To run ``Example.java`` in VSCode, make sure the Gradle extension is enabled and click on the Gradle icon in the sidebar then do ``playground-example → Tasks → application → run``.

## References

* [ETL Syntax](https://eclipse.dev/epsilon/doc/etl#concrete-syntax)
* [EOL Syntax](https://eclipse.dev/epsilon/doc/eol)