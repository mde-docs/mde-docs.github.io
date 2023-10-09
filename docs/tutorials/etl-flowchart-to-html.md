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

``` xml
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

``` xml
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

This ETL definition converts a flowchart model to an HTML document. Specifically, it turns all flowchart elements and sub-elements into headings. Each of the 4 rules only transform one type of element. The `Flowchart2Heading` rule transforms all flowchart elements into headings. The `Action2Heading` rule transforms all action elements into headings. The `Decision2Heading` rule transforms all decision elements into headings. The `Transition2Heading` rule transforms all transition elements into headings.

``` etl
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

Each of the rules defined above follow the same structure. The `transform` clause defines an input object with a type from the source metamodel. The `to` clause defines an output object with a type from the target metamodel. The `to` statment is followed by EOL statement(s) enclosed in curly braces. The EOL statements define the transformation logic. In this case, the transformation logic is to assign the name of the input element to the value of the output element.

### Inheritance

Inheritance in ETL allows you to reuse the same transformation logic for different elements. In the example below, the `Flowchart2H1` rule is abstract and is extended by the `Subflow2H1` rule. The `Subflow2H1` rule inherits the transformation logic of the `Flowchart2H1` rule and adds its transformation logic.

``` etl
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

Lazy rules are those which are invoked only if their output is requested by another rule using an equivalent operation. The intended purpose is to manage the execution of different rules, and restrict executing only those lazy rules whose output needs to be accessed by some other rule. With regards to rule priority, lazy rules are invoked after all non-lazy rules have been executed. In the example below, the `Action2Heading` and `Decision2Heading` rules are lazy and are invoked (in top-down order) after the `Flowchart2Heading` rule has been executed.

``` etl
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

In this example, three rules have been annotated as `@lazy`. However, only 2 of them (`Action2Heading` and `Decision2Heading`) are invoked. This is because lazy rules can only be applied if their result is requested using a `fetch()` operation (or `::=`) from another rule. `Action` and `Decision` elements inherit from `Node` elements. The `Flowchart2Heading` rule uses the `f.nodes.equivalent()` operation to fetch all `Node` elements (including `Action` and `Decision` elements) and transform them into `H1` elements. The `Transition2Heading` rule is never invoked because the `Flowchart2Heading` rule does not fetch any `Transition` elements.

### Greedy Execution

Greedy rules are executed for all instances of the input type (including sub-types). The difference between a regular rule and a greedy rule, is that a regular rule only applies to instances of the given input type and not the sub-types. Note that greedy rules are not specially prioritized during execution.

``` etl
//@greedy tag allows the rule to be applicable to input type and all sub-types
@greedy
rule NamedElement2Heading
	transform e : Source!NamedElement
	to h1 : Target!H1 {
	
	h1.value = e.name;
}
```

In the example above, the `NamedElement2Heading` rule is greedy (annotated using `@greedy` tag) and is executed for all instances of the `NamedElement` type (including sub-types). Every flowchart element inherits from the `NamedElement` meaning the rule applies to all elements in the source model. Thus, names of all flowchart elements are transformed into headings.

### Primary Annotation

Primary annotated rules are used to order the results of an equivalents() operation (defined in some other rule). The results of primary rules precede other rules. In the following example, the `Transition2Heading` rule is primary and its results precede other rules.

``` etl
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

This ETL definition shows that the `Flowchart2Heading` rule transforms a flowchart element into a `DIV` block. All transitions of the flowchart source model are fetched and added to the `contents` DIV block. When `equivalents()` operation is performed, all results of transformed `Transition` elements from other rules are returned. However, the rule annotated as `@primary` can supersede other rules in the result list and its result will be passed to the method call. In this case, the `Transition2Heading` rule is primary and its result will be passed to the `contents` DIV block. The other two rules (`Transition2SourceLink` and `Transition2TargetLink`) are not primary and their results will be ignored, they will still be executed but their results will not be in the `DIV` block. Note that `equivalents()` returns a sequence and `equivalents().first` returns the first item of the sequence not the results list.

### Equivalent Operation

Equivalent operation is mainly used for resolving the source elements and defining the mapping between input and output objects. When equivalents() operation is applied on a single source element, it would establish the transformation trace, invoke other rules (using the same source element) and return the newly transformed target elements. However, when equivalent() operation is applied, only the first result of the equivalents() operation is returned. When equivalent() operation is used on a collection, it returns a flattened collection instead of keeping more dimensions. In the example below, the `Flowchart2Div` rule transforms a `Flowchart` element into a `DIV` element. The `Transition2Heading` rule transforms a `Transition` element into an `H1` element. The `Flowchart2Div` rule uses the `equivalent()` operation to resolve the `Transition` elements of the `Flowchart` element.

``` etl
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

ETL uses a special EOL operator (`::=`) to replace `equivalent()` clause as it keeps the code more concise and readable. The `Flowchart2DIV` rule contains an output element `div` of the `DIV` type. `div` adds all the `Transition` elements of the `Flowchart` element using the `equivalent()` operation. Remember, `equivalent()` returns only the first result of the `equivalents()` operation. In this case, the `Transition2Heading` rule is executed and the `h1` element is returned for each of the transitions. The `h1` elements are then added to the `div` element. If there was another rule that transformed `Transition` elements, then the order of the rules would determine which rule's result is returned by the `equivalent()` operation.

### Multiple Targets

As the name suggests, in ETL the user can have one source element transforming to multiple target elements in one rule. All target elements are mapped to the same source element and can interact with other target elements of the rule. In the example below, the `Action2Elements` rule transforms an `Action` element into a `DIV`, `H1`, and `A` element. The `Decision2Elements` rule transforms a `Decision` element into a `DIV`, `H1`, and a sequence of `A` elements.

``` etl
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

In the first rule `Action2Elements`, each of the `Action` flowchart elements is transformed into a `DIV` block, `H1` heading and `A` reference link. All of the target elements resolve to the same source element `a`. The `guard` clause ensures that only `Action` elements with outgoing transitions are transformed. Additionally, all target elements can interact with each other in the EOL statments i.e. `container` DIV element adds `title` and `link` elements as its children. 

The second rule `Decision2Elements` transforms each of the `Decision` flowchart elements into a `DIV` block, `H1` heading and a sequence of `A` reference links. The `links` sequence is used to store all the `A` elements. The `for` loop iterates over all outgoing transitions of the `Decision` element and adds a new `A` element to the `links` sequence. The `links` sequence and `title` output variables are then added to the `container` DIV element.

## Development Platforms

Currently, the Flowchart to HTML project is available on the online [Epsilon playground](https://eclipse.dev/epsilon/playground/?inheritance&examples=https://raw.githubusercontent.com/iNafey/epsilon-playground/main/examples.json), where you can interact with all of the transformation examples documented above. 

