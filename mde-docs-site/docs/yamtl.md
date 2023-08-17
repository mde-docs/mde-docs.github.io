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

All rules are contained in a ``ruleStore()`` method as a list of ``rule()``s enclosed in square brackets ``[]``. A rule is instantiated using ``rule('<name>')`` with a rule name in the parentheses and single quotes. Each rule consists of one or more input element(s), defined using `in('<sourceObjectName>', <sourceObjectType>)` operation that requires a source element name and type; an optional filter condition expressed with ``filter(<FILTER>)`` where `<FILTER>` is a lambda expression; and one or more output element(s), declared with `out('targetObjectName', <targetObjectType>, {ACTION})` requiring a target element name and type, along with a block of lambda expression(s) enclosed in curly brackets ``{}`` containing action statements that initialize or update local variables. <br>

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
    [.endsWith(<ACTION>)]?
    [.priority(P)]?
```
**Note: <> is meant to show user-definable fields, []? means optional, []* means operation can occur 0 or more times, {}+ means operation can occur 1 or more times. These symbols are not part of the actual YAMTL syntax. Remember, {} is still part of the syntax.**

YAMTL has two types of input elements: **matched** and **derived**. Matched elements are initialized using YAMTL's matching algorithm, whereas derived elements are initialized using a contextual query and are dependent on at least one matched element. Intuitively, each rule has at least one matched input element as you would expect.

There are some optional parameters for additional customization. They will be discussed from top to bottom of the full syntax provided above. Each rule has various options to define its characteristics. The ``inheritsFrom(<ruleNameList>)`` operation is declared when the current rule inherits from parent rule(s) where ``ruleNameList`` is a comma-separated list of strings and the order of inheritance is specified sequentially. An optional ``abstract`` tag is used for abstract rules which cannot be matched automatically or applied. ``lazy`` rules are only applicable when the match for matched input elements is explicitly provided using the ``fetch`` command. The `fetch` operation uses an object resolution strategy based on internal traceability links and automatically does element mapping for the user. Keep in mind that the `fetch` operation fetches output objects whose corresponding () input objects  A `uniqueLazy` rule can only be applied if it has not been applied already for the same input match (``lazy`` rules don't have such a constraint). Notice, both ``lazy`` and `uniqueLazy` are not matched automatically. A rule defined as `transient` does not persist the target (output) elements when the target model flushes to physical storage.

An input element has multiple options to provide flexibility of use. ``filter(<FILTER>)`` clause enables the user to add a common local filter condition when multiple input elements need to be filtered. ``with(<nameList>)`` method is used to declare dependencies between matched input elements. Specifically, it allows you to use objects matched in previous input elements within the filter expression of the input elements that follow. ``<nameList>`` is a comma-separated list of strings that contain the names of objects you want to use in the filter expression. The ``derivedWith(<QUERY>)`` clause is used to declare an input element as **derived** where ``<QUERY>`` is a lambda expression of the 'EObject' type used to calculate the value of the match. 

Output elements also have some options. An ``overriding()`` qualifier is used to override inherited action expression(s) in the output element of a descendant rule. Elements that are used both as input and output can be removed using the ``.drop`` clause. It has delete cascade semantics that indicates both the object and its contents following containment references are removed.

Rules can also have some optionality at the end. The ``endsWith(<ACTION>)`` method is used to define an optional ``ACTION`` lambda expression which can refer to any of the rule's elements and any local variables. Note that the ``endsWith()`` method is purely for convenience: it facilitates the grouping of initialization of different output elements in a single code block. To change the priority of a rule, you can use the ``priority(P)`` operation where P is a 'long' value. Rules with lower priority are applied first by the YAMTL matching algorithm. Additionally, YAMTL provides attribute helpers for computing values during the initialization of the model transformation. The helpers are defined in the transformation's constructor. The ``helperStore()`` operation in the constructor contains the helper(s) as a list enclosed in square brackets `[]`. The helper syntax ``Helper('<helperName>')`` is used to define an attribute helper with the name in single quotes and is followed by a query lambda expression enclosed in square brackets``[]``. ``allInstances`` operation is used to create OCL-like queries in lambda expressions.


## Examples

* The [Linked list reversal](examples/linked-list-reversal-example.md) example reverses a linked list data structure originally stored in XMI format (source model). YAMTL transformation generates an ``outputList.xmi`` containing the target model. Both source and target metamodels are created using the same ECore file since the data structure remains the same after the transformation. A Gradle test runs a Groovy script that loads the input model, executes the transformation, and saves the output model.
