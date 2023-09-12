# YAMTL

Yet Another Model Transformation Language (YAMTL) is an expressive model-to-model transformation language that is offered as an internal domain-specific language (DSL) of JVM languages, including Java, Xtend, Groovy and Kotlin.

YAMTL is available as an IDE-agnostic Java dependency that augments the Java ecosystem with model analysis and model transformation capabilities that are not yet available in the latest version of Java. YAMTL transformations can be developed, debugged, and analyzed using the preferred Java IDE of choice and they can build upon existing Java dependencies to automate complex tasks. YAMTL operates on models defined with the Eclipse Modeling Framework.

YAMTL was found to be the fastest incremental model transformation tool, in general, for dealing with complex transformations between AADL models according to an independent industrial case study. 



## Getting Started

### What you will do
Create and set up a YAMTL project (without models and metamodels) that is ready for model transformations in an IDE of your choice.

### What you need

* An IDE (e.g. Eclipse, VSCode or IntelliJ)
* Java 17 or later (Minimum requirement)
* Gradle 8.0+ (Minimum requirement)
* Groovy plugin installed in your IDE (see [Workspace Configuration](#workspace-configuration) to install it)

### Choosing an IDE

To use YAMTL appropriately, an IDE must be properly configured. Let"s check out the required configurations for some of the most popular IDEs: Eclipse, IntelliJ, and VSCode.

#### Eclipse

Open Eclipse IDE and head over to ```Help → Eclipse Marketplace```. Enter "Groovy" and install ``Groovy Development Tools 5.0.0.RELEASE`` to be able to run Groovy scripts.

Before you run any tasks, make sure your project is using **JDK 17 or higher**.

??? info "How to change the Java version in Eclipse"
    To change your JRE, head over to ``Eclipse → Preferences → Java → Installed JREs → Choose Java SE 17 or higher``

Now you should be ready to use YAMTL in your modeling projects.

#### IntelliJ

Head over to ``IntelliJ IDEA → Preferences → Plugins`` and search for ``Eclipse Groovy Compiler Plugin`` and install it.

Similarly, search for "gradle" and install the ``Gradle`` plugin from JetBrains. Restart your IDE to apply the changes.

Ensure the project is using **JDK 17 or higher**.

??? info "How to change the Java version in IntelliJ"
    To change your JDK, head over to ``IntelliJ IDEA → Preferences → Build, Execution, Deployment → Build Tools → Gradle``. Then, select a ``Gradle JVM`` that is **JDK 17 or higher**.

All necessary configurations are now completed!

#### VSCode

First, a groovy support package must be installed. ``code-groovy`` extension enables Groovy support for VSCode. In VScode, click on ``Extensions`` and search for "code-groovy". Install the extension from **Marlon Franca**.

Also, install the ``Gradle for Java`` extension published by Microsoft to run the Gradle scripts in a neat interface.

Make sure the workspace is using **JDK 17 or higher**.

??? info "How to change the Java version in VSCode"
    To change your JRE, head over to ``Code → Preferences → Settings`` and search for "JDK". Check the Gradle ``Java: Home`` setting to see if the path points to a location of JDK 17 or higher (update the JDK version if it is any lower).

    ![JDK setting in VSCode](assets/images/jdk-vscode.png)

The configurations are completed! [Get started with YAMTL](#getting-started) by installing some dependencies.

### Walkthrough

First, you need to create a Gradle project in your IDE. Here, are the ways to do so in some common IDEs:

**Eclipse:** Create a new `Other` project. Then search for `Gradle Project`, choose a suitable starter project name, and hit ``Finish``.

**IntelliJ:** Go ``File → New → Project... → New Project``. Choose the language as ``Groovy``, build system as ``Gradle``, JDK as **17 or higher**, and Gradle DSL as ``Groovy``. 

**VSCode:** Do ``Shift+Cmd+P`` or ``Ctrl+Shift+P`` to open editor commands. Search and click on the `Gradle project` (may require `Gradle for Java` extension to be installed). Do ``Build script DSL as Groovy → New Project Name``. <br><br>

YAMTL uses Gradle as build automation tool and can be executed from Java-SE 17. To add YAMTL to your project you must configure the Gradle build script (``build.gradle``) of your project.
Add the Groovy plugin (at the top of the ``build.gradle`` file):
```
plugins {
    id "groovy"
}
```

Add the following repositories:
```
repositories {
	maven{ url "https://github.com/yamtl/yamtl.github.io/raw/mvn-repo/mvn-repo/snapshot-repo" }
	mavenCentral()
}
```

Then declare the dependencies (EMF dependencies are optional but since many metamodels use EMF format, it is advised you include it):
```
dependencies {
    // YAMTL dependencies
    implementation "yamtl:yamtl:${yamtlVersion}"

    implementation "org.apache.groovy:groovy-all:${groovyAllVersion}"
    implementation "org.eclipse.emf:org.eclipse.emf.ecore:${ecoreVersion}"
    implementation "org.eclipse.emf:org.eclipse.emf.ecore.xmi:${ecoreXmiVersion}"
    implementation "org.eclipse.emf:org.eclipse.emf.ecore.change:${ecoreChangeVersion}"
    implementation "org.eclipse.xtend:org.eclipse.xtend.core:${xtendVersion}"
    implementation "org.springframework.boot:spring-boot-starter-aop:${springAopVersion}"
    implementation "org.aspectj:org.aspectj:${aspectJVersion}"
}
```

The latest versions of the dependencies are defined in the ``build.gradle`` file can be below:

* Latest ``${yamtlVersion}`` is ``0.4.3``
* Find the latest ``${groovyAllVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.apache.groovy/groovy-all)
* Find the latest ``${ecoreVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.eclipse.emf/org.eclipse.emf.ecore)
* Find the latest ``${ecoreXmiVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.eclipse.emf/ecore-xmi)
* Find the latest ``${ecoreChangeVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.eclipse.emf/org.eclipse.emf.ecore.change)
* Find the latest ``${xtendVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.eclipse.xtend/org.eclipse.xtend.core)
* Find the latest ``${springAopVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-aop)
* Find the latest ``${aspectJVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.aspectj/aspectjweaver)

Finally, build the project to install the dependencies. 

You are now ready to use your YAMTL project! Check out the [examples](#examples) section to learn model transformations of varying difficulties using YAMTL.

## Basic Syntax

A YAMTL model transformation is defined as a class that specializes the `YAMTLModule` class, which provides access to the YAMTL DSL and to methods to configure and execute model transformations:

=== "Groovy"

    ```groovy
    class <name> extends YAMTLModule {
        public <name> (EPackage <pk1>, EPackage <pk2>) {
            YAMTLGroovyExtensions_dynamicEMF.init(this)
            header().in(<in_domain_name1>,<pk1>).out(<in_domain_name2>,<pk2>)
            ruleStore([ /* rules here */ ])
            helperStore([  /* managed helpers here */ ])
        }
    }
    ```

=== "Xtend"

    ```xtend
    class <name> extends YAMTLModule {
        new(EPackage <pk1>, EPackage <pk2>) {
            header().in(<in_domain_name1>,<pk1>).out(<in_domain_name2>,<pk2>)
            ruleStore(#[ /* rules here */ ])
            helperStore(#[ /* managed helpers here */ ])
        }
    }
    ```

=== "Java"

    ```java
    public class <name> extends YAMTLModule {
        public <name>(EPackage <pk1>, EPackage <pk2>) {
            header().in(<in_domain_name1>,<pk1>).out(<in_domain_name2>,<pk2>);
            ruleStore(List.of( /* rules here */ ));
            helperStore(List.of( /* managed helpers here */ ));
        }
    }
    ```

=== "Kotlin"

    ```kotlin
    class <name>(<pk1>: EPackage, <pk2>: EPackage) : YAMTLModule() {
        init {
            header().`in`(<in_domain_name1>,<pk1>).out(<in_domain_name2>,<pk2>)
            ruleStore(listOf( /* rules here */ ))
            helperStore(listOf( /* managed helpers here */ ))
        }
    }
    ```



In the code above there are four important sections:

* **Constructor** signature: It should include the different metamodels (`EPackage` instances) used in the transformation. 
*   **Header**: This section declares the signature of the model transformation using a unique name for each domain and its corresponding metamodel, which can be shared across domains.  
*   **Rule Store**: This section declares a list of transformation rules.
*   **Helper Store (Optional)**: Accepts a list of managed helpers. Managed helpers are attributes or methods that are optimized in YAMTL using an internal cache for their results. Unmanaged helpers are declared as standard methods of the module class. This section is optional if no managed helpers are needed.

The basic format of a YAMTL rule definition is as follows:

```
rule("<name>")
    .in("<in_object_name>", <in_object_type>)[.derivedWith(<QUERY>)]?[.filter(<FILTER>)]?
    .out("<out_object_name>", <out_object_type>, <ACTION>)
```
!!! info "Legend"

    `<>` indicates user-definable expressions. Note that these are placeholders and not part of the actual YAMTL syntax. Lowercase snake case (e.g., `in_object_name`) usually denotes variable names and types, including lists of variable names. Uppercase snake case (e.g., `<FILTER>` or `<ACTION>`) represents lambda expressions, and they are written using the syntax of the host language. `[]?` means optional.

A rule is declared using `rule("<name>")` with a rule name. The static operation `rule` can be used with `import static yamtl.dsl.Rule.*`. Each rule consists of one or more input element(s), defined using `in("<in_object_name>", <in_object_type>)` operation that requires a source element name and type; an optional `.derivedWith(<QUERY>)` clause where `<QUERY>` is a lambda expression of type `Supplier<EObject>` that produces the object that will be matched to the input element; an optional filter condition expressed with `filter(<FILTER>)` where `<FILTER>` is a lambda expression of type `Supplier<Boolean`; and one or more output element(s), declared with `out("out_object_name", <out_object_type>, <ACTION>)` requiring a target element name and type, along with a side-effecting lambda expression `<ACTION>` of type `Runnable` containing action statements that initialize or update the output object attributes and references. <br>


## YAMTL Semantics

Model transformations can be used to define model queries by using pattern matching, out-place model transformation by *mapping* an input model into a *new* output model, or in-place model transformations by *rewriting* a given model.

### Pattern Matching Semantics

Pattern matching is the process by which YAMTL tries to find object graphs in the input model where the input pattern of a rule can be matched. 

There are two main types of input elements: **matched** elements, which are mapped by YAMTL to objects in the input model, and **derived** elements, which are defined with a `.derivedWith(<QUERY>)` clause and need to be derived from input elements that have been matched in preceding input elements. 

To find a match for a rule, YAMTL first maps each matched input element of the rule to objects in the input model in the order in which they appear. For derived elements, YAMTL tries to complete the total match by processing query expressions in the order that they were declared. If a query cannot be resolved to an object, that rule"s match is invalid. 

A match for a matched rule must be **unique**. That is, no other rule should be applicable to the same match. Uniqueness of matches is checked at runtime using the flag `YAMTLModule::setEnableCorrectnessCheck(Boolean)`, which is `true` by default. Non-unique matches are allowed when using (unique) lazy rules, which are called on demand.

A match is **complete** when all input elements are mapped to objects, either implicitly via matched input elements or explicitly via derived input elements. A match is defined as a map where the key is the input variable name and the value is the corresponding matched `EObject`.

!!! tip "Model-sensitive pattern matching"

    The input elements are ordered by the size of their type extent (smaller-sized types are matched before bigger ones) when enabling the flag `YAMTLModule::setEnabledMatchingInputElementOrderBySize(true)`. This can lead to significant run-time improvements when the distribution of objects across types is imbalanced.
    
    This optimization can, however, cause problems when the order of the input elements alters the order in which input element declaration is expected in filter expressions. For example, assuming that `Type1` declares a boolean method `isEnabled()` and that `Type2` has fewer objects than `Type1`, the order of input elements in the following input pattern

    ```
    .in("a", Type1)
    .in("b", Type2).filter{ a.isEnabled() }
    ```

    will be changed by the flag `setEnabledMatchingInputElementOrderBySize`. This will cause a problem because the input element `b` will be evaluated first and its filter condition needs "a" to be matched first. In such cases, the flag `YAMTLModule::setEnabledMatchingInputElementOrderBySize` must be kept disabled.


A match for a rule is **valid** when it is unique, complete and all of the filters of the input pattern are satisfied. Filters come in two flavours:

* **Local filters**: defined for an input element `.in("<in_object_name>", <in_object_type>).filter(<FILTER>)`. The lambda expression `<FILTER>` can use any object variable declared in a preceding input element.
* **Global filters**: defined for the last input element of the input pattern. All input object variables can be used for defining the filter expression. 

!!! tip "Design principles for efficient pattern matching"

    1. Matched input elements should only be defined for matching objects that are not related to each other through references (if they are then they should be defined as derived elements instead).

    2. Local element filter conditions should be opted for instead of global rule filter conditions to help the matching algorithm remove invalid matches as soon as possible (reduces execution time).

    3. Once it is known that only unique matches are found within a model for a given set of rules, the model transformation containing them can be executed more efficiently by disabling the uniqueness correctness check with `YAMTLModule::setEnableCorrectnessCheck(false)`.

YAMTL"s pattern matcher can be used to implement model queries. A model query is a rule that only has an input pattern and that may have a final action block `endWith(<ACTION>)`:

```
rule("<name>")
    .in("<in_object_name>", <in_object_type>)[.derivedWith(<QUERY>)]?[.filter(<FILTER>)]?
    .query()
    [.endWith(<ACTION>)]?
```

The `<ACTION>` in `endWith(<ACTION>)` is a lambda expression of type `Runnable` that may use the input element variables to perform some action on the input objects that have been matched, e.g., reporting error messages or computing metrics.

To configure and execute a YAMTL module for implementing rule-based queries, use the following template:

```groovy
def mm = BaseQuery.loadMetamodel("<path_to_metamodel>")
def query = new BaseQuery(mm)
query.selectedExecutionPhases = ExecutionPhase.MATCH_ONLY
query.loadInputModels(["<in_domain_name>": "<path_to_model>"])
query.execute()
```

When using dynamic EMF for accessing metamodel metadata (i.e., EMF code has not been generated for the metamodel), use the static method `YAMTLModule::loadMetamodel("<path_to_metamodel>")`, which works with both Ecore files (`.ecore`) and with [EMFatic](https://eclipse.dev/emfatic/) files (`.emf`) to load the metamodel. Then instantiate the YAMTL module containing the model query, configure it to execute only the matching phase, load the input models and, finally, execute the query using the method `YAMTLModule::execute()`. 


### Out-place Model-to-Model Transformation Semantics

YAMTL modules are typically used to specify model-to-model transformations, where the objects of an input model are mapped to objects of an output model that is created **from scratch**. This is commonly referred to as out-place transformation because the input model is read-only and not modified.

Side effects in a model transformation are specified in the `out` elements of rules. For each transformation rule that has been matched, the rule is applied by creating an object in the output model for each `out` element and the object is initialized using the corresponding `<ACTION>` expression. Within a rule, an `ACTION` expression can refer to:

* the input object variables (either matched or derived) of that rule, 
* `using` variables of that rule, and 
* all output object variables of that rule.

When an expression needs to reference output objects that are initialized by other rules, the operator `YAMTLModule::fetch()` needs to be used. The primary purpose of the `fetch` operator is to retrieve output objects corresponding to a given input object through the application of a transformation rule. The simplest version is suitable for matched rules that have a single object pattern in both the input and output patterns: `fetch(input_matched_object)` will return the output object created by the rule that matched `input_matched_object`.

Matched rules can be declared with the `toMany` modifier. This allows for repeated rule applications to the same input object, subject to a valid termination condition `toManyCap` based on the match count. With `toMany` rules, the same rule might match the same object multiple times. In such cases, we can reference each match (occurrence 'i' of a match) by the order in which they occurred: `fetch(<input_matched_object>, <i>)` will return the output object created by the ith match.

When the output pattern comprises several object patterns, it's necessary to specify which output element we wish to fetch: `fetch(<input_matched_object>, <outVarName>)` will return the output object linked to the output element `outVarName`. If a matched rule with a complex output pattern also uses the `toMany` declaration, the output object can be retrieved with `fetch(input_matched_object, out_object_name, i)`.

A lazy rule, whether unique or non-unique, requires explicit invocation to produce an output element. This is executed by using the rule name as follows:

*   `fetch(<input_matched_object>, <out_object_name>, <rule_name>)` for lazy rules with a single input object and multiple output objects.
*   `fetch(<input_matched_object>, <out_object_name>, <rule_name>, <i>)` for `toMany` lazy rules with a single input object and multiple output objects.

When the input pattern contains more than one element, instead of using one single input object, a [valid match](#pattern-matching-semantics) must be provided by using a map where the keys are ``<in_object_name>``s and the values are the matched `EObject`s.


!!! warning 
    
    In a rule with an output element `.out(<out_object_name>, <out_object_type>, <ACTION>)`, the expression ``<ACTION>`` should only be used to initialize the output object of type `<out_object_type>` that is created by this output element.



!!! danger "TODO"
    
    Rules with additional arguments.


### In-place Model Transoformation Semantics


!!! danger "TODO"
    
    When an output element is defined with a new name it creates a new instance of the initialized object. Alternatively, when an output element refers to an input element i.e. input element is also an output element, the element is updated and no new object is created. In both cases, the mapping from input match to output match is traced as a transformation step. Ad-hoc objects that are manually created using an object factory and assigned to an output element in the ``ACTION`` expression, are not traced by YAMTL. This means such non-traceable objects cannot be fetched from another rule.



## Lazy Rules

## Helpers

A helper in YAMTL streamlines the writing of transformation rules by offering reusable expressions. Think of it as creating utility functions or methods in conventional programming languages.

In YAMTL, you can define helpers using standard constructs from the host programming language:

*   **Attributes** with initialization expressions.
*   **Static operations** that apply at the class level across all instances.
*   **Operations** specific to objects.

YAMTL further boosts these helpers' utility by caching their computations, optimizing runtime performance. Below, we present how to declare these helpers and call them in your transformations.

### Attribute Helpers

The method `staticAttribute("<name>", <BODY>)` creates an attribute `<name>`. Its value gets determined by the `<BODY>` expression, which must be of type `Supplier<Object>`.

Attribute helpers shine when used with the `allInstances(<EClass>)` operator. This operator fetches a list containing all instances of the type `<EClass>` present in the input model. The expression `<BODY>` must return the value used to initialize the attribute, which can be an `EObject` or a primitive value.

=== "Groovy"

    ```groovy
    staticAttribute("<AttributeName>", {  
        // an expression returning a value from allInstances(<InputEClass>)
    })
    ```

=== "Xtend"

    ```xtend
    staticAttribute("<AttributeName>", [|  
        // an expression returning a value from allInstances(<InputEClass>)
    ])
    ```

=== "Java"

    ```java
    staticAttribute("<AttributeName>", new Supplier<Object>() {  
        @Override
        public Object get() {
            // an expression returning a value from allInstances(<InputEClass>)
        }
    });
    ```

=== "Kotlin"

    ```kotlin
    staticAttribute("<AttributeName>") {  
        // an expression returning a value from allInstances(<InputEClass>)
    }
    ```

An attribute helper can then be called by name. While the YAMTL Groovy DSL allows us to consider the attribute helper as a variable using its name (without the String quotes) directly, the operator `fetch` needs to be used in all other programming languages:

=== "Groovy"

    ```groovy
    <AttributeName>
    ```

=== "Xtend"

    ```xtend
    fetch("<AttributeName>")
    ```

=== "Java"

    ```java
    fetch("<AttributeName>")
    ```

=== "Kotlin"

    ```kotlin
    fetch("<AttributeName>")
    ```

### Static Operation

To manage static methods, YAMTL uses `staticOperation("<name>", <FUNCTION>)` to define an operation `<name>` where `<FUNCTION>` is a lambda expression with a list of parameters specified as a map. The keys in the map are the names of the parameters, and the values are the actual arguments. Within the body of the lambda expression, you can access the arguments map using `argMap` and must ensure to return a value.

=== "Groovy"

    ```groovy
    staticOperation("<OperationName>", { argMap -> 
        // returns the value of the parameter with name <param_name>
        argMap.<param_name> 
    })
    ```

=== "Xtend"

    ```xtend
    staticOperation("<OperationName>", [ argMap | 
        // returns the value of the parameter with name <param_name>
        argMap.get("<param_name>")
    ])
    ```

=== "Java"

    ```java
    staticOperation("<OperationName>", argMap -> {
        // returns the value of the parameter with name <param_name>
        return argMap.get("<param_name>");
    });
    ```

=== "Kotlin"

    ```kotlin
    staticOperation("<OperationName>") { argMap ->
        // returns the value of the parameter with name <param_name>
        argMap["<param_name>"]
    }
    ```

Static operations are invoked by their names and the list of arguments, specifying the name of the parameter and the actual argument value. While the YAMTL Groovy DSL allows calling the static operation directly, all other programming languages require the `fetch` operator:

=== "Groovy"

    ```groovy
    <OperationName>(["<param1>" : <value1>, ...])
    ```

=== "Xtend"

    ```xtend
    fetch("<OperationName>", #["<param1>" -> <value1>, ...])
    ```

=== "Java"

    ```java
    fetch("<OperationName>", Map.of("<param1>", <value1>, ...));
    ```

=== "Kotlin"

    ```kotlin
    fetch("<OperationName>", mapOf("<param1>" to <value1>, ...))
    ```

### Contextual Operation

To manage class methods, YAMTL uses `contextualOperation("<name>", <BIFUNCTION>)` to define an operation `<name>` where `<BIFUNCTION>` is a lambda expression with two parameters: the contextual instance or object to which the operation is applied, and list of parameters specified as a map. The keys in the map are the names of the parameters, and the values are the actual arguments. Within the body of the lambda expression, you can access the contextual instance or the arguments map, and must ensure to return a value, either an `EObject` or a primitive value.

=== "Groovy"

    ```groovy
    staticOperation("<OperationName>", { obj, argMap -> 
        // to access the contextual instance use 'obj' 
        // to access an argument use 'argMap.<param_name>' 
        // must return a value
    })
    ```

=== "Xtend"

    ```xtend
    contextualOperation("<OperationName>", [ obj, argMap | 
        // to access the contextual instance use 'obj' 
        // to access an argument use 'argMap.get("<param_name>")' 
        // must return a value
    ])
    ```

=== "Java"

    ```java
    contextualOperation("<OperationName>", (obj, argMap) -> {
        // to access the contextual instance use 'obj'
        // to access an argument use 'argMap.get("<param_name>")'
        // must return a value
    });
    ```

=== "Kotlin"

    ```kotlin
    contextualOperation("<OperationName>") { obj, argMap ->
        // to access the contextual instance use 'obj'
        // to access an argument use 'argMap["<param_name>"]'
        // must return a value
    }
    ```

Contextual operations are invoked on the `<ContextualInstance>` using the `<OperationName>` and the list of arguments, specifying the name of the parameter and the actual argument value. While the YAMTL Groovy DSL allows calling the  operation directly, all other programming languages require the `fetch` operator:

=== "Groovy"

    ```groovy
    <OperationName>(<ContextualInstance>, ["<param1>" : <value1>, ...])
    ```

=== "Xtend"

    ```xtend
    <ContextualInstance>.fetch("<OperationName>", #["<param1>" -> <value1>, ...])
    ```

=== "Java"

    ```java
    fetch(<ContextualInstance>, "<OperationName>", Map.of("<param1>", <value1>, ...));
    ```

=== "Kotlin"

    ```kotlin
    fetch(<ContextualInstance>, "<OperationName>", mapOf("<param1>" to <value1>, ...))
    ```

## Other components

YAMTL is as expressive as ATL so it also has a lot of optional operations. These options provide a more thorough (full) syntax for the language.

```
rule("<name>")
    [.inheritsFrom(<ruleNameList>)]? 
    [.isAbstract()]? 
    [.isLazy() | .isUniqueLazy()]? 
    [.isTransient()]?
    {
        .in("<in_object_name>", <in_object_type>)
        [(.filter(<FILTER>) | .derivedWith(<QUERY>))]?
    }+
    [.filter(<FILTER>)]?
    {
        .out("<out_object_name>", <out_object_type>, <ACTION>)
        [.overriding()]?
        [.drop()]?
    }+
    [.endWith(<ACTION>)]?
    [.priority(P)]?
```
**Note: <> indicates user-definable expressions, []? means optional, []* means operation can occur 0 or more times, {}+ means operation can occur 1 or more times. These symbols are not part of the actual YAMTL syntax.**

YAMTL has two types of input elements: **matched** and **derived**. Matched elements are initialized using YAMTL"s matching algorithm, whereas derived elements are initialized using a contextual query and are dependent on at least one matched element. Intuitively, each rule has at least one matched input element as you would expect.

Every rule has optional parameters for additional customization. They will be discussed from top to bottom of the full syntax provided above. The ``inheritsFrom(<ruleNameList>)`` operation is declared when the current rule inherits from parent rule(s) where ``ruleNameList`` is a comma-separated list of strings and the order of inheritance is specified sequentially. An optional ``abstract`` tag is used for abstract rules which cannot be matched automatically or applied. ``lazy`` rules are only applicable when the match for matched input elements is explicitly provided using the ``fetch`` command. The `fetch` operation uses an object resolution strategy based on internal traceability links and automatically does element mapping for the user. Keep in mind that the `fetch` operation fetches output objects whose corresponding input objects have been mapped by another rule. A `uniqueLazy` rule can only be applied if it has not been applied already for the same input match (``lazy`` rules do not have such a constraint). Notice, both ``lazy`` and `uniqueLazy` are not matched automatically. A rule defined as `transient` does not persist the target (output) elements when the target model flushes to physical storage.

An input element has multiple options to provide flexibility of use. ``filter(<FILTER>)`` clause enables the user to add a local filter condition that needs to be satisfied by the matched object of the corresponding input element. ``<nameList>`` is a comma-separated list of strings that contain the names of objects you want to use in the filter expression. The ``derivedWith(<QUERY>)`` clause is used to declare an input element as **derived** where ``QUERY`` is a lambda expression of the "EObject" type used to calculate the value of the match. 

A global filter condition for a rule can be added after the input element block using `filter(<FILTER>)` clause which allows the user to add filter(s) applicable to the global scope of the rule.

Output elements also have some options. An ``overriding()`` qualifier is used to override inherited action expression(s) in the output element of a descendant rule. Elements that are used both as input and output can be removed using the ``.drop()`` clause. It has delete cascade semantics that indicates both the object and its contents following containment references are removed.

Rules can also have some optionality at the end. The ``endWith(<ACTION>)`` method is used to define an optional ``<ACTION>`` lambda expression which can refer to any of the rule"s elements and any local variables. Note that the ``endWith()`` method is purely for convenience: it facilitates the grouping of initialization of different output elements in a single code block. 

To change the priority of a rule, you can use the ``priority(P)`` operation where P is a "long" value. Rules with lower priority are applied first by the YAMTL matching algorithm. Additionally, YAMTL provides attribute helpers for computing values during the initialization of the model transformation. The helpers are defined in the transformation"s constructor. 

The ``helperStore()`` operation in the constructor contains the helper(s) as a list enclosed in square brackets `[]`. The helper syntax ``Helper("<helperName>")`` is used to define an attribute helper with the name in single quotes and is followed by a query lambda expression enclosed in square brackets``[]``. ``allInstances(<typeName>)`` operation is used to create OCL-like queries in lambda expressions.

## YAMTL Semantics



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

When a specializing module declares a rule that is already defined in the parent module, the new rule redefines the existing one if they have compatible signatures. This means that the importing module"s rule (child module) defines all elements of the rule from the imported module (parent module), and the type of each of such element in the child module must be a subtype of the type of the element in the parent module. This ensures that the specialization maintains compatibility with the base rule.

Attribute helpers (``Helper()``) are redefined when extending modules because they do not have any parameters. Imported rules and attribute helpers that are redefined cannot be accessed any longer.

## Examples

* The [Linked list reversal](examples/linked-list-reversal-example.md) example reverses a linked list data structure originally stored in XMI format (source model). YAMTL transformation generates an ``outputList.xmi`` containing the target model. Both source and target metamodels are created using the same ECore file since the data structure remains the same after the transformation. A Gradle test runs a Groovy script that loads the input model, executes the transformation, and saves the output model.
