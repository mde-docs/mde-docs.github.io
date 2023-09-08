# Flowchart to HTML - ETL Implementation

In this tutorial, the core concepts of ETL transformations will be described. To do this, elements of a flowchart model will be transformed using special operations into valid HTML elements.

<figure markdown style="height:350px;width:400px">
  ``` mermaid
  graph LR
    A[Begin] -->|Start| B[Wake up];
    B --> C{Is it really too early?};
    C --> |Yes| D[Sleep];
    C --> |No| E[Get up]
    D --> |Some Time Passes| B;
  ```
  <figcaption>Wakeup Flowchart</figcaption>
</figure>

The flowchart model (abstract representation shown in the figure above) has different types of elements: Node, Action, Decision, Transition, and Subflow.

**Node** elements are the most basic elements in a flowchart. They can have any number of incoming and outgoing transitions.

**Action** elements are a type of Node element. They have a value describing an action and such elements can also have any number of incoming and outgoing transitions.

**Decision** elements are another type of Node element. Its value indicates a decision (if-else condition) and a decision element can have any number of incoming and outgoing transitions.

**Transition** elements are those arrows (direction flow) in the flowchart figure above. They have a name attribute. A transition element **must** have one source Node (start of the arrow) and one target Node (end of the arrow).

**Subflow** is a special Node element that contains a flowchart inside a flowchart (making it a sub-section). A subflow element has a name and all the features of a flowchart i.e. can have any number of nodes and transitions.

## Source Metamodel

This is the flowchart metamodel in Emfatic `.emf` as defined in the [Flowchart to HTML](../examples/flowchart-to-html-example.md#source-metamodel) case before.

## Target Metamodel

This is the HTML metamodel in Emfatic `.emf` as defined in the [Flowchart to HTML](../examples/flowchart-to-html-example.md#target-metamodel) case before.

## Source Model

### Wakeup Flowchart

The flowchart diagram can be represented in Flexmi with some changes (outgoing and incoming transitions of `Node` elements are removed because they cause syntactical errors for nodes with multiple transitions):

```
<flowchart>
    <action name="Wake up"/>
    <decision name="Is it too early?"/>
    <action name="Sleep"/>
    <action name="Get up"/>
    <transition source="Wake up" target="Is it too early?"/>
    <transition name="yes" source="Is it too early?" target="Sleep"/>
    <transition name="some time passes" source="Sleep" target="Wake up"/>
    <transition name="no" source="Is it too early?" target="Get up"/>
</flowchart>
```

### Wakeup Flowchart with Subflow

Some MTL examples also transform the Flowchart's subflow elements, so, another flowchart (that contains subflow) based on the original flowchart is also defined. The flowchart model with subflow is as follows:

**Graphical Representation**

![Wakeup flowchart with Subflow](../assets/images/wakeup-with-subflow.png)

**Abstract Syntax in Flexmi**

```
<flowchart>
    <action name="Wake up"/>
    <action name="Get up"/>
    <action name="Begin"/>
    <subflow name="Snoozing">
        <action name="Sleep"/>
        <decision name="Is it too early?"/>
        <transition name="Yes" source="Is it too early?" target="Sleep"/>
    </subflow>
    <transition source="Wake up" target="Is it too early?"/>
    <transition name="Some time passes" source="Sleep" target="Wake up"/>
    <transition name="No" source="Is it too early?" target="Get up"/>
    <transition name="Start" source="Begin" target="Wake up"/>
</flowchart>
```

## Transformation Examples

### Base Example

This ETL definition converts a flowchart model to an HTML document. Specifically, it turns all flowchart elements and sub-elements into headings.

```
//This rule transforms all flowchart elements to headings
rule Flowchart2Heading
	transform f : Source!Flowchart
	to h1 : Target!H1 {
	
	h1.value = f.name;
}

//This rule transforms all action elements to headings
rule Action2Heading
	transform a : Source!Action
	to h1 : Target!H1 {
	
	h1.value = a.name;
}

//This rule transforms all decision elements to headings
rule Decision2Heading
	transform d : Source!Decision
	to h1 : Target!H1 {
	
	h1.value = d.name;
}

//This rule transforms all transition elements to headings
rule Transition2Heading
	transform t : Source!Transition
	to h1 : Target!H1 {
	
	h1.value = t.name;
}
```

### Inheritance

Inheritance in ETL allows you to reuse the same transformation logic for different elements. In the example below, the `Flowchart2H1` rule is abstract and is extended by the `Subflow2H1` rule. The `Subflow2H1` rule inherits the transformation logic of the `Flowchart2H1` rule and adds its transformation logic.

```
// If we make the following rule abstract, then only
// subflows will be transformed.
@abstract
rule Flowchart2H1
	transform e : Source!Flowchart
	to h1 : Target!H1 {
	
	//Assigns the name of the flowchart to the value of the h1 element
	h1.value = "Flowchart " + e.name; 
}

//Transform subflows into headings
rule Subflow2H1
	transform e : Source!Subflow
	to h1 : Target!H1
	extends Flowchart2H1 { //Subflow2H1 inherits from Flowchart2H1
	
	h1.value = "Subflow " + h1.value;
}
```

When the `Flowchart2H1` rule is abstract, only subflows will be transformed and output in the target model. When the `Flowchart2H1` rule is not abstract, all flowchart elements will be transformed. In this case, the `Subflow2H1` rule inherits from `Flowchart2H1` and substitutes the name of `e` input element (`e.name`) into the parent rule (note `e` input element is the same as `e` input element in the parent rule). Since `Subflow2H1` rule extends `Flowchart2H1` rule, the parent rule is also executed and the `e` element from the child is passed to the parent rule during execution, causing the value of `h1` in the parent rule to be "Flowchart Snoozing". This is also reflected when the `Subflow2H1` rule's output pattern is executed and its `h1` value will be "Subflow Flowchart Snoozing". 

### Lazy Execution

Lazy rules are invoked after all non-lazy rules have been executed. In the example below, the `Action2Heading` and `Decision2Heading` rules are lazy and are invoked (in top-down order) after the `Flowchart2Heading` rule has been executed.

```
rule Flowchart2Heading
	transform f : Source!Flowchart
	to div : Target!DIV {
	
	div.children.addAll(f.nodes.equivalent());
}

// Lazy rules are invoked only when their output is 
// requested by some other rule using equivalent() or ::=
@lazy
rule Action2Heading 
	transform a : Source!Action
	to h1 : Target!H1 {
	
	h1.value = a.name;
}

@lazy
rule Decision2Heading 
	transform d : Source!Decision
	to h1 : Target!H1 {
	
	h1.value = d.name;
}

// Note that the following rule is never invoked
@lazy
rule Transition2Heading 
	transform t : Source!Transition
	to h1 : Target!H1 {
	
	h1.value = t.name;
}

```

### Greedy Execution

Greedy rules are invoked before all non-greedy rules have been executed. In the example below, the `NamedElement2Heading` rule is greedy and is invoked before other rules are executed.

```
//This rule is used to transform a NamedElement into a Heading.

//@greedy tag prioritizes execution of this rule over other rules.
@greedy
rule NamedElement2Heading
	transform e : Source!NamedElement
	to h1 : Target!H1 {
	
	h1.value = e.name;
}
```

### Primary Annotation

Rules with `primary` annotation are used to order the results of the `equivalent()` operation. In the example below, the `Transition2Heading` rule is primary and its results precede other rules.

```
rule Flowchart2Heading
	transform f : Source!Flowchart
	to contents : Target!DIV {
	
	// Produce a DIV containing only the headings of the transitions
	// and not containing the links
	for (t in f.transitions) {
		contents.children.add(t.equivalents().first);
	}
}

rule Transition2SourceLink
	transform t : Source!Transition
	to a : Target!A {
	
	a.value = t.source.name;
	a.ahref = "#" + t.source.name;
}

rule Transition2TargetLink
	transform t : Source!Transition
	to a : Target!A {
	
	a.value = t.name;
	a.ahref = "#" + t.target.name;
}

//Results of equivalents() can be ordered using rules that are declared @primary
//Primary rule's results precede other rules
@primary
rule Transition2Heading 
	transform t : Source!Transition
	to h1 : Target!H1 {
	
	h1.value = t.name;
}
```

### Equivalent Operation

The `equivalent()` operation is used to resolve source elements and define the mapping between source and target objects. In the example below, the `Flowchart2Div` rule transforms a `Flowchart` element into a `DIV` element. The `Transition2Heading` rule transforms a `Transition` element into an `H1` element. The `Flowchart2Div` rule uses the `equivalent()` operation to resolve the `Transition` elements of the `Flowchart` element.

```
//This rule is used to transform a Flowchart into a DIV
rule Flowchart2Div
	transform f : Source!Flowchart
	to div : Target!DIV {
	
	//The "::=" operator is the same as equivalent() method.
	//This equivalent() method returns a flattened list of all the transitions.
	div.children ::= f.transitions; 
	
	// The preceeding line is the same as:
	//   div.children.addAll(f.transitions.equivalent());
	//
	// And also the same as:
	//   for (t in f.transitions) {
	//     div.children.add(t.equivalent());
	//   }
}

//This rule is used to transform a Transition into a Heading.
rule Transition2Heading
	transform t : Source!Transition
	to h1 : Target!H1 {
	
	h1.value = t.name;
}
```

ETL uses a special EOL operator (`::=`) to replace `equivalent()` clause as it keeps the code more concise and readable.

### Multiple Targets

In ETL, you can have rules with multiple target elements. In the example below, the `Action2Elements` rule transforms an `Action` element into a `DIV`, `H1`, and `A` element. The `Decision2Elements` rule transforms a `Decision` element into a `DIV`, `H1`, and a sequence of `A` elements.

```
rule Action2Elements
	transform a : Source!Action
	to container : Target!DIV, title : Target!H1, link : Target!A { 
	//Note only one source element is transformed into 3 targets

	//"Get up" is not transformed as it has no outgoing transitions
	guard: a.outgoing.notEmpty() 
	
	title.value = a.name;
	link.value = "Next steps";
	link.ahref = a.outgoing.first.target.name;
	
	container.children.add(title);
	container.children.add(link);
}


rule Decision2Elements
	transform d : Source!Decision
	to container : Target!DIV, title : Target!H1, links : Sequence(Target!A) {
	
	title.value = d.name;
	
	for (t in d.outgoing) {
		var link = new Target!A;
		link.value = t.target.name;
		link.ahref = t.target.name;
		links.add(link);
	}
	
	container.children.add(title);
	container.children.addAll(links);
}
```

## Development Platforms

Currently, the Flowchart to HTML project is available on the online [Epsilon playground](https://eclipse.dev/epsilon/playground/?inheritance&examples=https://raw.githubusercontent.com/iNafey/epsilon-playground/main/examples.json), where you can interact with all of the transformation examples documented above. 

