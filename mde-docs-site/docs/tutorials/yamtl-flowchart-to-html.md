# Flowchart to HTML - YAMTL Implementation

In this tutorial, the core concepts of YAMTL transformations will be described. To do this, elements of a flowchart model will be transformed using special operations into valid HTML elements.

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

Let's turn that flowchart into an XMI representation because that is the required format for source models in YAMTL (note that this is equivalent to the Flexmi code shown on the previous page):

```
<?xml version="1.0" encoding="UTF-8"?>
<flowchart:Flowchart xmi:version="2.0" xmlns:xmi="http://www.omg.org/XMI" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:flowchart="flowchart" xmi:id="_9mLMwDY6EeOwt8pm-kjW_Q" name="Wakeup">
  <nodes xsi:type="flowchart:Action" xmi:id="_9mLMwTY6EeOwt8pm-kjW_Q" name="Wake up" outgoing="_9mLMxjY6EeOwt8pm-kjW_Q" incoming="_9mLMyDY6EeOwt8pm-kjW_Q _9mLz0TY6EeOwt8pm-kjW_Q"/>
  <nodes xsi:type="flowchart:Decision" xmi:id="_9mLMwjY6EeOwt8pm-kjW_Q" name="Is it really too early?" outgoing="_9mLMxzY6EeOwt8pm-kjW_Q _9mLz0DY6EeOwt8pm-kjW_Q" incoming="_9mLMxjY6EeOwt8pm-kjW_Q"/>
  <nodes xsi:type="flowchart:Action" xmi:id="_9mLMwzY6EeOwt8pm-kjW_Q" name="Sleep" outgoing="_9mLMyDY6EeOwt8pm-kjW_Q" incoming="_9mLMxzY6EeOwt8pm-kjW_Q"/>
  <nodes xsi:type="flowchart:Action" xmi:id="_9mLMxDY6EeOwt8pm-kjW_Q" name="Get up" incoming="_9mLz0DY6EeOwt8pm-kjW_Q"/>
  <nodes xsi:type="flowchart:Action" xmi:id="_9mLMxTY6EeOwt8pm-kjW_Q" name="begin" outgoing="_9mLz0TY6EeOwt8pm-kjW_Q"/>
  <transitions xmi:id="_9mLMxjY6EeOwt8pm-kjW_Q" name="" source="_9mLMwTY6EeOwt8pm-kjW_Q" target="_9mLMwjY6EeOwt8pm-kjW_Q"/>
  <transitions xmi:id="_9mLMxzY6EeOwt8pm-kjW_Q" name="Yes" source="_9mLMwjY6EeOwt8pm-kjW_Q" target="_9mLMwzY6EeOwt8pm-kjW_Q"/>
  <transitions xmi:id="_9mLMyDY6EeOwt8pm-kjW_Q" name="Some Time Passes" source="_9mLMwzY6EeOwt8pm-kjW_Q" target="_9mLMwTY6EeOwt8pm-kjW_Q"/>
  <transitions xmi:id="_9mLz0DY6EeOwt8pm-kjW_Q" name="No" source="_9mLMwjY6EeOwt8pm-kjW_Q" target="_9mLMxDY6EeOwt8pm-kjW_Q"/>
  <transitions xmi:id="_9mLz0TY6EeOwt8pm-kjW_Q" name="start" source="_9mLMxTY6EeOwt8pm-kjW_Q" target="_9mLMwTY6EeOwt8pm-kjW_Q"/>
</flowchart:Flowchart>
```

## Transformation Examples

### Basic Example

This is a simple example that converts all flowchart elements into HTML `H1` headings:

```
ruleStore([

                rule('Flowchart2Heading')
                        .in("f", flowchartPk.Flowchart)
                        .out("h1", htmlPk.H1, {
                            h1.value = f.name
                        }),
                rule('Action2Heading')
                        .in("a", flowchartPk.Action)
                        .out("h1", htmlPk.H1, {
                            h1.value = a.name
                        }),
                rule('Decision2Heading')
                        .in("d", flowchartPk.Decision)
                        .out("h1", htmlPk.H1, {
                            h1.value = d.name
                        }),
                rule('Transition2Heading')
                        .in("t", flowchartPk.Transition)
                        .out("h1", htmlPk.H1, {
                            h1.value = t.name
                        })
        ])
```

There are 4 rules that transform different flowchart objects: Flowchart, Action, Decision, and Transition; into H1 elements. Each rule has a name (within `rule('<ruleName>')` clause), an input element (with a name and type) and an output element (with a name, type and a lamda expression). The lambda expression of all rules follow the same format: assign the name of the input object to the value of the heading `H1`.

### Inheritance

First, an abstract rule is defined with a set of input and output elements. Then, a child rule is declared which inherits from the abstract rule (parent) and performs the transformation.

```
ruleStore([

                rule('Flowchart2H1')
                        .isAbstract()
                        .in("e", flowchartPk.Flowchart)
                        .out("h1", htmlPk.H1, {
                            h1.value = "Flowchart " + e.name
                        }),

                rule('Subflow2H1')
                        .inheritsFrom(['Flowchart2H1'])
                        .in("e", flowchartPk.Subflow)
                        .out("h1", htmlPk.H1, {
                            h1.value = "Subflow " + h1.value
                        })
        ])
```

``isAbstract()`` clause is used to define an abstract rule. The abstract rule contains an input element of type `Flowchart` and an output element of H1 heading. The output object's value is updated to the input object's name with a prefix "Flowchart". A new child rule that inherits from the abstract rule using the `inheritsFrom(['<ruleNameList>'])` clause. The child rule `Subflow2H1` has an input element of type `Subflow` which extends `Flowchart`. Its name is `e` just like in the parent rule, meaning when the child rule is executed the input object `e`  overrides the object `e` in the parent rule. The output element's type must be the same as the parent rule. The output object's value is "Subflow " followed by the value of `h1` in the abstract rule because both rules are executed and the output objects are calculated. Thus, the final output is an H1 element of the following format:

```
<H1 value="Subflow Flowchart Snoozing"/>
```

### Lazy Rules

A rule is one which is executed after all non-lazy and non-abstract rules.

```
ruleStore([
            rule('Flowchart2Heading')
                .in('f', flowchartPk.Flowchart)
                .out('div', htmlPk.DIV, {
                    // without LAZY: div.children.addAll(fetch(f.nodes))
					// with LAZY rules
					div.children.addAll(fetch(f.nodes, 'out', 'NodeRule'))
                }),
            rule('Transition2H1')
				.in("t", flowchartPk.Transition)
				.out("h1", flowchartPk.H1, {
					h1.value = t.name
				}),
			rule('NodeRule')
				.isUniqueLazy()
				.in("in", flowchartPk.Node)
				.out("out", htmlPk.H1, {
					out.value = in.name
				})
        ])
```

### Transient Rules


### Local Filter

In this example, a filter condition (which is a lambda expression) is applied to a rule to transform selected input objects.

```
ruleStore([
				rule('SelectedTransitions2Text')
						.in("t", flowchartPk.Transition)
						.filter{	
							//Filter input objects that satify this condition
                            t.source.name == "Is it really too early?"		
						}
						.out("p", htmlPk.P, {
							p.value = t.name
						})
		])
```

The rule `SelectedTransitions2Text` has an input element as a `Transition` object. A filter condition is applied to check the name of the transition's source. If the transition source name is "Is it really too early?" then only those input `Transition` objects will be transformed. The output element is a HTML paragraph element `p` whose value is updated to the name of the input `Transition` object.

### Derived Input Elements

<!-- Need to complete this -->


### Multiple Sources

<!-- Need to complete this -->

### Multiple Targets

<!-- Need to complete this -->

### Rule Override

There may be cases where you need to override the output object of the parent rule. This means that when the rule that inherits is executed, the value of the output object in the parent rule is overriden by the value of the output object calculated in the child rule. This also means that the child output object has no value at the start of execution unlike when it is inherited with no override.

```
ruleStore([
				rule('Flowchart2H1')
                        .in("e", flowchartPk.Flowchart)
                        .out("h1", htmlPk.H1, {
							//Assigns the name of the flowchart to the value of the h1 element
                            h1.value = "Flowchart " + e.name 
                        }),

                rule('Subflow2H1')
                        .inheritsFrom(['Flowchart2H1'])
                        .in("e", flowchartPk.Subflow)
                        .out("h1", htmlPk.H1, {
							
							//If h1 value is inherited then it is not null, else it is null
							if(h1.value !== null) {
								h1.value = "Subflow " + h1.value
							} else {
								h1.value = "Subflow " + e.name //Overridden output object
							}
                        //Override the parent rule's h1 output object so the one in child rule is used    
                        }).overriding() 
		])
```

To better understand the properties of the `overriding()` clause, you should see the difference in execution and output, when you use override and when you do not.

When you **use** `overrding()`:

The rule that inherits (`Subflow2H1`), overrides the parent rule's (`Flowchart2H1`) output object `h1` meaning its value is newly initialised (`null`), thus, the else-condition is invoked. The local input object `e` is referenced within the output block and its name (`e.name`) is retrieved to be assigned as part of a string to the value of `h1`. The output of this transformation would look like:

```
<H1 value="Flowchart Wakeup"/>
<H1 value="Subflow Snoozing"/>
```

When you **do not use** `overriding()`:

When the child rule is executed, the output element(s) of the parent rule is calculated first. This means that `h1` output object has a computed value in the parent rule. When you access the `h1` output object in the child rule, it references to the parent rule's `h1` output object (which contains a value). Thus, the if-condition is satisfied and the child rule's output object `h1` is assigned the value of a string and the value of `h1` object calculated within the parent rule. The main output of this transformation would be:

```
<H1 value="Flowchart Wakeup"/>
<H1 value="Subflow Flowchart Snoozing"/>
```

### End With Block

If you want elements of a rule to interact with each other, you can do so at the end of a rule execution using an optional operation called `endWith`. An `endWith` block allows the user to group all elements of a rule, update objects and perform calculations using lamda expressions.

```
ruleStore([
				rule('Flowchart2Body') //Adds all flowchart elements into the HTML body
						.endWith{
							body.text = f.name //Body's text field has the flowchart name
							body.children.add(b) //The newly created bold text is also added to the body
							body.children.add(div)	//New div block is also added			 
						}
						.in("f", flowchartPk.Flowchart) //Input object is all flowchart elements
						.out("b", htmlPk.B, { 
							b.value = f.name //Flowchart's name is turned into bold
						})
						.out("div", htmlPk.DIV, { 
							div.children.addAll(f.transitions) //A div block contains all transitions
						})
						.out("body", htmlPk.BODY, {
							body.children.addAll(f.nodes) //All flowchart nodes are added to the body
						})
		])
```

In the transformation example above, one source element `f` is transformed into multiple targets: `b` is a `Bold` HTML element which is assigned the flowchart's name as its value, `div` output object has all transitions of the flowchart `f` as its children, and `body` output object has all flowchart `f` nodes as its children. Once, all output blocks are executed, the `endWith` block is invoked. In the `endWith` block, a lamda expression is defined that updates the `body` output object's text field with the `f` flowchart's name and the `body` object also adds new children: `b` output object and `div` output object. These updates are shown in the output, found in the target model.


### Rule Priority

As the title suggests, you can prioritize rules to be executed in the order you prefer using the `priority(P)` clause where `P` is a whole number (e.g. 0, 1, 2,...) and the rules are executed in ascending values of ``P`` i.e. rules with lower `P` values have higher priority. 

```
ruleStore([
				rule('Flowchart2Title')
					.priority(1)
					.in("f", flowchartPk.Flowchart)
					.out("title", htmlPk.TITLE, {
						title.value = f.name 
					}),
				rule('Action2Heading')
					.priority(3)
					.in("a", flowchartPk.Action)
					.out("h2", htmlPk.H2, {
						h2.value = "H2 heading for Action: " + a.name
					}),			
				rule('Decision2Heading')
					.priority(2)
					.in("d", flowchartPk.Decision)
					.out("h1", htmlPk.H1, {
						h1.value = "H1 heading for Decision: " + d.name 
					}),
				rule('Transition2Heading')
					.priority(4)
					.in("t", flowchartPk.Transition)
					.out("h3", htmlPk.H3, {
						h3.value = "H3 heading for Transition: " + t.name
					})
					
				
		])
```

In the MT definition above, the flow of execution for all the rules is:

1. `Flowchart2Title`
1. `Decision2Heading`
1. `Action2Heading`
1. `Transition2Heading`
   
So you can expect the output of this transformation to also be ordered in the above manner within the target model.

### Helpers

<!-- Need to complete this -->

### Model Queries

<!-- Need to complete this -->

### Module Composition

<!-- Need to complete this -->

## Transformation Test Script

The test script is responsible for loading source and target metamodels, initializing the MT definition, loading soure model into the transformation and executing it, and saving the output in the target model.

```
// model transformation execution flow
def src_metamodel = YAMTLModule.loadMetamodel(BASE_PATH + '/flowchart.ecore') as EPackage
def tgt_metamodel = YAMTLModule.loadMetamodel(BASE_PATH + '/html.ecore') as EPackage

def xform = new <groovyClass>(src_metamodel, tgt_metamodel)
xform.loadInputModels(['in': BASE_PATH + '/wakeup.xmi'])
xform.execute()
xform.saveOutputModels(['out': BASE_PATH + '/<outputFileName>.xmi'])
```

First, both source and target metamodels are loaded using a `YAMTLModule` function called `loadMetamodel(<'projectPath'>)` and a type `EPackage` is applied because that is how `.ecore` files and their components are accessed in the project. Note that `BASE_PATH` is just a global variable (with the value 'model' in this case) containing base directory name. `xform` variable initializes a new MT definition if a valid Groovy class name and parameters (source and target metamodels) are provided. Next, the input model is loaded using project path and the transformation is executed. The output is saved within the target model in the location (path) provided.

## Development Platforms

Download the [Flowchart to HTML](../assets/downloads/.zip) project (ZIP file). You can unzip and import the project into an IDE of your choice. The following steps will provide details on how to set up the example project on Eclipse, IntelliJ, and VSCode.

YAMTL uses Groovy scripts to define the models and transformations so generally any IDE will need some Groovy support through extensions/plug-ins. Make sure to have YAMTL correctly configured or do the necessary steps found in the [YAMTL Workspace Configuration](../yamtl.md#workspace-configuration) section before you run the project.

### **Eclipse**

Unzip the downloaded project. In Eclipse, click on ``File → Import → Existing Projects into Workspace → Select root directory → Finish``. This will import and load the project in the IDE.

Right-click on a test script of your choice in the ``src/test/groovy`` folder then ``Run as → JUnit Test``. Once the test is completed, a new output file (XMI format) will be generated in the ``model`` directory. Examine this file.

### **IntelliJ**

Within IntelliJ, go to ``File → Open`` and open the project.

Right-click on ``src/test/groovy/flowchartToHtmlExamples`` folder then 'Run Tests...'. 

Alternatively, click on the green run button in the top bar which builds the project and generates output files of all transformations in the ``model`` directory. 

### **VSCode**

In VSCode, import the project by doing ``File → Open``.

After you have imported the example project into the workspace, click on the Gradle icon in the left sidebar. Then perform ``Tasks →  build → clean``. Do ``Tasks → build → build`` to build the entire Gradle project (it also runs all test scripts that execute the transformations). 

Once the project is successfully built, all output files will be generated in the ``model`` directory. Examine these files.

## References

* [YAMTL Syntax](https://dl.acm.org/doi/10.1145/3239372.3239386)
* [YAMTL Incremental Support](https://link.springer.com/article/10.1007/s10009-020-00583-y)
* [YAMTL Original Documentation](https://arturboronat.github.io/yamtl/)