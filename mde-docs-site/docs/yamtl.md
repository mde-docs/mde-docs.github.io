# YAMTL

Yet Another Model Transformation Language (YAMTL) is an expressive model-to-model transformation language that is offered as an internal domain-specific language (DSL) of Java/Xtend. YAMTL was found to be the fastest incremental model transformation tool, in general, for dealing with complex transformations between AADL models according to an independent industrial case study. YAMTL is available as an IDE-agnostic Java dependency that augments the Java ecosystem with model analysis and model transformation capabilities that are not yet available in the latest version of Java. YAMTL transformations can be developed, debugged, and analyzed using the preferred Java IDE of choice and they can build upon existing Java dependencies to automate complex tasks. YAMTL operates on models defined with the Eclipse Modeling Framework.

## Workspace Configuration

To use YAMTL appropriately, the IDE must be properly configured. Let's check out the required configurations for some of the most popular IDEs: Eclipse, IntelliJ, and VSCode.

### Eclipse

Open Eclipse IDE and head over to ```Help → Eclipse Marketplace```. Enter "Groovy" and install ``Groovy Development Tools 5.0.0.RELEASE`` to be able to run Groovy scripts.

Before you run any tasks, make sure your project is using **JDK 17 or higher**.

??? info "How to change the Java version in Eclipse"
    To change your JRE, head over to ``Eclipse → Preferences → Java → Installed JREs → Choose Java SE 17 or higher``

Now you should be ready to use YAMTL in your modeling projects.

### IntelliJ

Head over to ``IntelliJ IDEA → Preferences → Plugins`` and search for ``Eclipse Groovy Compiler Plugin`` and install it.

Similarly, search for "gradle" and install the ``Gradle`` plugin from JetBrains. Restart your IDE to apply the changes.

Ensure the project is using **JDK 17 or higher**.

??? info "How to change the Java version in IntelliJ"
    To change your JDK, head over to ``IntelliJ IDEA → Preferences → Build, Execution, Deployment → Build Tools → Gradle``. Then, select a ``Gradle JVM`` that is **JDK 17 or higher**.

All necessary configurations are now completed!

### VSCode

First, a groovy support package must be installed. ``code-groovy`` extension enables Groovy support for VSCode. In VScode, click on ``Extensions`` and search for "code-groovy". Install the extension from **Marlon Franca**.

Also, install the ``Gradle for Java`` extension published by Microsoft to run the Gradle scripts in a neat interface.

Make sure the workspace is using **JDK 17 or higher**.

??? info "How to change the Java version in VSCode"
    To change your JRE, head over to ``Code → Preferences → Settings`` and search for "JDK". Check the Gradle ``Java: Home`` setting to see if the path points to a location of JDK 17 or higher (update the JDK version if it is any lower).

    ![JDK setting in VSCode](assets/images/jdk-vscode.png)

The configurations are completed! [Get started with YAMTL](#getting-started) by installing some dependencies.

## Getting Started

### What you will do
Create and set up a YAMTL project (without models and metamodels) that is ready for model transformations in an IDE of your choice.

### What you need

* An IDE (e.g. Eclipse, VSCode or IntelliJ)
* Java 17 or later (Minimum requirement)
* Gradle 8.0+ (Minimum requirement)
* Groovy plugin installed in your IDE (see [Workspace Configuration](#workspace-configuration) to install it)

### Walkthrough

First, you need to create a Gradle project in your IDE. Here, are the ways to do so in some common IDEs:

**Eclipse:** Create a new `Other` project. Then search for `Gradle Project`, choose a suitable starter project name, and hit ``Finish``.

**IntelliJ:** Go ``File → New → Project... → New Project``. Choose the language as ``Groovy``, build system as ``Gradle``, JDK as **17 or higher**, and Gradle DSL as ``Groovy``. 

**VSCode:** Do ``Shift+Cmd+P`` or ``Ctrl+Shift+P`` to open editor commands. Search and click on the `Gradle project` (may require `Gradle for Java` extension to be installed). Do ``Build script DSL as Groovy → New Project Name``. <br><br>

YAMTL uses Gradle as build automation tool and can be executed from Java-SE 17. To add YAMTL to your project you must configure the Gradle build script (``build.gradle``) of your project.
Add the Groovy plugin (at the top of the ``build.gradle`` file):
```
plugins {
    id 'groovy'
}
```

Add the following repositories:
```
repositories {
	maven{ url 'https://github.com/yamtl/yamtl.github.io/raw/mvn-repo/mvn-repo/snapshot-repo' }
	mavenCentral()
}
```

Then declare the dependencies (EMF dependencies are optional but since many metamodels use EMF format, it is advised you include it):
```
dependencies {
    // YAMTL dependencies
    implementation 'yamtl:yamtl:${yamtlVersion}'
    implementation 'yamtl:untyped-models:${untypedModelsVersion}'

    // https://mvnrepository.com/artifact/org.apache.groovy/groovy-all
    implementation 'org.apache.groovy:groovy-all:${groovyAllVersion}'

    // https://mvnrepository.com/artifact/org.eclipse.emf/org.eclipse.emf.ecore
    implementation 'org.eclipse.emf:org.eclipse.emf.ecore:${ecoreVersion}'

    // https://mvnrepository.com/artifact/org.eclipse.emf/ecore-xmi
    implementation 'org.eclipse.emf:org.eclipse.emf.ecore.xmi:${ecoreXmiVersion}'

    // https://mvnrepository.com/artifact/org.eclipse.emf/org.eclipse.emf.ecore.change
    implementation 'org.eclipse.emf:org.eclipse.emf.ecore.change:${ecoreChangeVersion}'

    // https://mvnrepository.com/artifact/org.eclipse.xtend/org.eclipse.xtend.core
    implementation 'org.eclipse.xtend:org.eclipse.xtend.core:${xtendVersion}'

    // https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-aop
    implementation 'org.springframework.boot:spring-boot-starter-aop:${springAopVersion}'
}
```

The latest versions of the dependencies are defined in the ``build.gradle`` file can be below:

* Latest ``${yamtlVersion}`` is ``0.4.3``
* Latest ``${untypedModelsVersion}`` is ``0.0.25``
* Find the latest ``${groovyAllVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.apache.groovy/groovy-all)
* Find the latest ``${ecoreVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.eclipse.emf/org.eclipse.emf.ecore)
* Find the latest ``${ecoreXmiVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.eclipse.emf/ecore-xmi)
* Find the latest ``${ecoreChangeVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.eclipse.emf/org.eclipse.emf.ecore.change)
* Find the latest ``${xtendVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.eclipse.xtend/org.eclipse.xtend.core)
* Find the latest ``${springAopVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-aop)

Finally, build the project to install the dependencies. 

You are now ready to use your YAMTL project! Check out the [examples](#examples) section to learn model transformations of varying difficulties using YAMTL.

## Concrete Syntax

The basic format of a YAMTL definition based on a Groovy script is as follows:
```
rule('<name>')
    .in('<sourceObjectName>', <sourceObjectType>)
    .filter(<FILTER>)?
    .out('<targetObjectName>', <targetObjectType>, {
        //Action block
    })*
```
**Note: <> is meant to show user-definable fields and is not part of the actual syntax**

YAMTL transformation module begins by creating a specialization of the class `YAMTLModule`. The module's constructor contains the `header()` which defines the signature of the transformation (where you declare the input and output models) and the ``ruleStore()`` method containing rules as a list of ``rule()``s enclosed in square brackets ``[]``. A rule is instantiated using ``rule('<name>')`` with a rule name in the parentheses and single quotes. Each rule consists of one or more input element(s), defined using `in('<sourceObjectName>', <sourceObjectType>)` operation that requires a source element name and type; an optional filter condition expressed with ``filter(<FILTER>)`` where `FILTER` is a lambda expression; and one or more output element(s), declared with `out('targetObjectName', <targetObjectType>, {ACTION})` requiring a target element name and type, along with a block of lambda expression(s) enclosed in curly brackets ``{}`` containing action statements that initialize or update local variables. <br>

YAMTL is as expressive as ATL so it also has a lot of optional operations. These options provide a more thorough (full) syntax for the language.

```
rule('<name>')
    [.inheritsFrom(<ruleNameList>)]? 
    [.abstract]? 
    [.lazy | .uniqueLazy]? 
    [.transient]?
    {
        .in('<sourceObjectName>', <sourceObjectType>)
        [.with(<nameList>)]?
        [(.filter(<FILTER>) | .derivedWith(<QUERY>))]?
    }+
    [.filter(<FILTER>)]?
    {
        .out('<targetObjectName>', <targetObjectType>, {
        // Action block
        })
        [.overriding()]?
        [.drop]?
    }+
    [.endWith(<ACTION>)]?
    [.priority(P)]?
```
**Note: <> is meant to show user-definable fields, []? means optional, []* means operation can occur 0 or more times, {}+ means operation can occur 1 or more times. These symbols are not part of the actual YAMTL syntax.**

YAMTL has two types of input elements: **matched** and **derived**. Matched elements are initialized using YAMTL's matching algorithm, whereas derived elements are initialized using a contextual query and are dependent on at least one matched element. Intuitively, each rule has at least one matched input element as you would expect.

Every rule has optional parameters for additional customization. They will be discussed from top to bottom of the full syntax provided above. The ``inheritsFrom(<ruleNameList>)`` operation is declared when the current rule inherits from parent rule(s) where ``ruleNameList`` is a comma-separated list of strings and the order of inheritance is specified sequentially. An optional ``abstract`` tag is used for abstract rules which cannot be matched automatically or applied. ``lazy`` rules are only applicable when the match for matched input elements is explicitly provided using the ``fetch`` command. The `fetch` operation uses an object resolution strategy based on internal traceability links and automatically does element mapping for the user. Keep in mind that the `fetch` operation fetches output objects whose corresponding input objects have been mapped by another rule. A `uniqueLazy` rule can only be applied if it has not been applied already for the same input match (``lazy`` rules do not have such a constraint). Notice, both ``lazy`` and `uniqueLazy` are not matched automatically. A rule defined as `transient` does not persist the target (output) elements when the target model flushes to physical storage.

An input element has multiple options to provide flexibility of use. ``filter(<FILTER>)`` clause enables the user to add a common local filter condition when multiple input elements need to be filtered. ``with(<nameList>)`` method is used to declare dependencies between matched input elements. Specifically, it allows you to use objects matched in previous input elements within the filter expression of the input elements that follow. ``<nameList>`` is a comma-separated list of strings that contain the names of objects you want to use in the filter expression. The ``derivedWith(<QUERY>)`` clause is used to declare an input element as **derived** where ``QUERY`` is a lambda expression of the 'EObject' type used to calculate the value of the match. Global filter condition for a rule can be added after the input element block using `filter(<FILTER>)` clause which allows the user to add filter(s) applicable to the global scope of the rule.

Output elements also have some options. An ``overriding()`` qualifier is used to override inherited action expression(s) in the output element of a descendant rule. Elements that are used both as input and output can be removed using the ``.drop`` clause. It has delete cascade semantics that indicates both the object and its contents following containment references are removed.

Rules can also have some optionality at the end. The ``endWith(<ACTION>)`` method is used to define an optional ``ACTION`` lambda expression which can refer to any of the rule's elements and any local variables. Note that the ``endWith()`` method is purely for convenience: it facilitates the grouping of initialization of different output elements in a single code block. To change the priority of a rule, you can use the ``priority(P)`` operation where P is a 'long' value. Rules with lower priority are applied first by the YAMTL matching algorithm. Additionally, YAMTL provides attribute helpers for computing values during the initialization of the model transformation. The helpers are defined in the transformation's constructor. The ``helperStore()`` operation in the constructor contains the helper(s) as a list enclosed in square brackets `[]`. The helper syntax ``Helper('<helperName>')`` is used to define an attribute helper with the name in single quotes and is followed by a query lambda expression enclosed in square brackets``[]``. ``allInstances`` operation is used to create OCL-like queries in lambda expressions.

## YAMTL Semantics

### Dispatch Semantics

This section will describe the matching procedure of rules in the MT definition. To find a match for a rule, YAMTL first maps matched input elements to objects which satisfy the corresponding filter condition in the order defined in `with` dependencies. This simply means in the clause ``.in(element1, type1).with([element2])``, ``element2`` is matched earlier than ``element1``. The user must carefully choose the order of precedence among input elements using `with` clauses because YAMTL will throw a run-time exception when trying to fetch an element that has not been matched yet. If there are no `with` dependencies, the input elements are ordered by the size of their type extent (smaller-sized types are matched before bigger ones). For derived elements, YAMTL tries to complete the total match by processing query expressions in the order that they were declared. If a query cannot be resolved to an object, that rule's match is invalid. However, if a total match is found, the rule filter condition asserts that the match is satisfied by returning ``[true]`` which is the default value (even when a filter condition is not manually declared). Note that matched elements take precedence over derived elements so they are traversed first.

!!! info "Design principles for efficient YAMTL use"
    1. Matched input elements should only be defined for matching objects that are not related to each other through references (if they are then they should be defined as derived elements instead).

    2. Local element filter conditions should be opted for instead of global rule filter conditions to help the matching algorithm remove invalid matches as soon as possible (reduces execution time).

### Execution Semantics

Generally, output elements are either *initialized* or *updated* during a transformation. 

When an output element is defined with a new name it creates a new instance of the initialized object. Alternatively, when an output element refers to an input element i.e. input element is also an output element, the element is updated and no new object is created. In both cases, the mapping from input match to output match is traced as a transformation step. Ad-hoc objects that are manually created using an object factory and assigned to an output element in the ``ACTION`` expression, are not traced by YAMTL. This means such non-traceable objects cannot be fetched from another rule.


### Multiple Rule Inheritance

The rule inheritance semantics differ for **matched** and **derived** input elements. In **matched** input elements, local filter expressions (``filter(<FILTER>)``) are inherited using a leftmost top-down evaluation strategy concerning the inheritance hierarchy defined in the ``inheritsFrom(<ruleNameList>)`` clauses (where the rules in the list are sequentially ordered). Derivation expressions (``derivedWith(<QUERY>)``) in **derived** input elements are overridden if declared else inherited.

Regarding output elements, action expressions (`ACTION`) are inherited through a leftmost top-down evaluation strategy for the inheritance hierarchy by default. These expressions can be overridden by defining an ``overriding()`` qualifier at the end of an output element of a descendant rule.

In a **specialized** rule, YAMTL has a few requirements:

* The parent rule elements must be declared in the descendant rule

* Abstract rules must be specialized.

* Concrete rules (non-abstract) should have elements in the output pattern (`out()`) that are typed with concrete classes.

If these constraints are violated, a declaration error is thrown. When a specialized rule inherits the same output element from two different parent rules, YAMTL displays a warning to the user but the model transformation still proceeds.

### Module Composition

YAMTL modules can be imported and used in other Xtend/Java/Groovy classes by creating instances of their main classes. This allows you to reuse the functionality provided by a YAMTL module within your code. A YAMTL module can also incorporate any Java Virtual Machine (JVM) library, extending its functionality by utilizing external code.

Module extension is used for composing modules i.e. creating a subclass of an existing module to extend the capabilities of the base module. When YAMTL modules are extended, the process of initializing rules and attribute helpers begins from the leaf modules (those that do not extend any other module). Initialization then proceeds along the hierarchy of extended modules, moving from parent modules to their descendants.

When a specializing module declares a rule that is already defined in the parent module, the new rule redefines the existing one if they have compatible signatures. This means that the importing module's rule (child module) defines all elements of the rule from the imported module (parent module), and the type of each of such element in the child module must be a subtype of the type of the element in the parent module. This ensures that the specialization maintains compatibility with the base rule.

Attribute helpers (``Helper()``) are redefined when extending modules because they do not have any parameters. Imported rules and attribute helpers that are redefined cannot be accessed any longer.

## Examples

* The [Linked list reversal](examples/linked-list-reversal-example.md) example reverses a linked list data structure originally stored in XMI format (source model). YAMTL transformation generates an ``outputList.xmi`` containing the target model. Both source and target metamodels are created using the same ECore file since the data structure remains the same after the transformation. A Gradle test runs a Groovy script that loads the input model, executes the transformation, and saves the output model.
