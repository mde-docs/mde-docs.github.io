# YAMTL

Yet Another Model Transformation Language (YAMTL) is an expressive model-to-model transformation language that is offered as an internal domain-specific language (DSL) of Java/Xtend. YAMTL was found to be the fastest incremental model transformation tool, in general, for dealing with complex transformations between AADL models according to independent industrial case study. YAMTL is available as an IDE-agnostic Java dependency that augments the Java ecosystem with model analysis and model transformation capabilities that are not yet available in the latest version of Java. YAMTL transformations can be developed, debugged and analysed using the preferred Java IDE of choice and they can build upon existing Java dependencies in order to automate complex tasks. YAMTL operates on models defined with the Eclipse Modeling Framework.

## Workspace Configuration

To use YAMTL appropriately, the IDE must be properly configured. Let's check out the required configurations for some of the most popular IDEs: Eclipse, IntelliJ, and VSCode.

### Eclipse

Open Eclipse IDE and head over to ```Help → Eclipse Marketplace```. Enter "groovy" and install ``Groovy Development Tools 5.0.0.RELEASE`` to be able to run groovy scripts.

Before you run any tasks, make sure your project is using **JDK 17 or higher**.

??? info "How to change the Java version in Eclipse"
    To change your JRE, head over to ``Eclipse → Preferences → Java → Installed JREs → Choose Java SE 17 or higher``

Now you should be ready to use YAMTL in your modelling projects.

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
    To change your JRE, head over to ``Code → Preferences → Settings`` and search for "jdk". Check the Gradle ``Java: Home`` setting to see if the path points to a location of JDK 17 or higher (update the JDK version if it is any lower).

    ![JDK setting in VSCode](assets/images/jdk-vscode.png)

The congifurations are completed! [Get started with YAMTL](#getting-started) by installing some dependencies.

## Getting Started

### What you will do

Create and setup a YAMTL project (without models and metamodels) that is ready for model transformations in an IDE of your choice.

### What you need

* An IDE (e.g. Eclipse, VSCode or IntelliJ)
* Java 17 or later (Minimum requirement)
* Gradle 8.0+ (Minimum requirement)
* Groovy plugin installed in your IDE (see [Workspace Configuration](#workspace-configuration) to install it)

### Walkthrough

First, you need to create a Gradle project in your IDE. Here, are the ways to do so in some common IDEs:

**Eclipse:** Create a new `Other` project. Then search for `Gradle Project`, choose a suitable starter project name and hit ``Finish``.

**IntelliJ:** Go ``File → New → Project... → New Project``. Choose language as ``Groovy``, build system as ``Gradle``, JDK as **17 or higher** and Gradle DSL as ``Groovy``. 

**VSCode:** Do ``Shift+Cmd+P`` or ``Ctrl+Shift+P`` to open editor commands. Search and click on ``Gradle project`` (may require `Gradle for Java` extension to be installed). Do ``Build script DSL as Groovy → New Project Name``. <br><br>

YAMTL uses Gradle as build automation tool and can be executed from Java-SE 17. To add YAMTL to your own project you must configure the Gradle build script (build.gradle) of your project.

Add the groovy plugin (at the top of the ``build.gradle`` file):
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
    implementation 'org.springframework.boot:spring-boot-starter-aop:${sbsAopVersion}'
}
```

The latest versions for the dependencies defined in the ``build.gradle`` file can be below:

* Latest ``${yamtlVersion}`` is ``0.4.3``
* Latest ``${untypedModelsVersion}`` is ``0.0.25``
* Find the latest ``${groovyAllVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.apache.groovy/groovy-all)
* Find the latest ``${ecoreVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.eclipse.emf/org.eclipse.emf.ecore)
* Find the latest ``${ecoreXmiVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.eclipse.emf/ecore-xmi)
* Find the latest ``${ecoreChangeVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.eclipse.emf/org.eclipse.emf.ecore.change)
* Find the latest ``${xtendVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.eclipse.xtend/org.eclipse.xtend.core)
* Find the latest ``${sbsAopVersion}`` on [Maven Central](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-aop)

Finally, build the project to install the dependencies. 

You are now ready to use your YAMTL project! Check out the [examples](#examples) section to learn model transformations of varying difficulties using YAMTL.

## Examples

* [Linked list reversal](examples/linked-list-reversal-example.md) project reverses a linked list data structure originally stored in XMI format (source model). YAMTL transformation generates an ``outputList.xmi`` containing the target model. Both source and target metamodels are created using the same ECore file since the data structure remains the same after the transformation. A Gradle test runs a Groovy script that loads the input model, executes the transformation and saves the output model.