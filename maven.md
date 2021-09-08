Maven
=====
Contents
--------
* [Maven Basics](#maven-basics)
    * [Maven Coordinates](#maven-coordinates)
    * [Snapshot Versions](#snapshot-versions)
    * [Maven Repositories](#maven-repositories)
    * [Dependency Scope](#dependency-scope)
    * [Maven Standard Directory Layout](#maven-standard-directory-layout)
* [Maven Build Lifecycles](#maven-build-lifecycles)
    * [How to list available goals of a plugin](#how-to-list-available-goals-of-a-plugin)
    * [Pre-defined lifecycles](#pre-defined-lifecycles)
    * [Clean lifecycle](#clean-lifecycle)
    * [Default lifecycle](#default-lifecycle)
        * [Default lifecycle at high-level](#default-lifecycle-at-high-level)
        * [Skipping tests](#skipping-tests)
        * [Default lifecycle all phases](#default-lifecycle-all-phases)
        * [Default Lifecycle - JAR packaging](#default-lifecycle---jar-packaging)
* [Common Maven Plugins](#common-maven-plugins)
    * [Maven Clean Plugin](#maven-clean-plugin)
    * [Maven Compiler Plugin](#maven-compiler-plugin)
    * [Other Plugins](#other-plugins)
    * [Generating Source with Maven](#generating-source-with-maven)
        * [XSD to Java](#xsd-to-java)
        * [JSON to Java](#json-to-java)
        * [Use MapStruct to generate Java classes](#use-mapstruct-to-generate-java-classes)
* [Alternative JVM Languages](#alternative-jvm-languages)
    * [Compile Groovy with Maven](#compile-groovy-with-maven)
    * [Compile Kotlin with Maven](#compile-kotlin-with-maven)
    * [Compile Scala with Maven](#compile-scala-with-maven)
* [Testing with Maven](#testing-with-maven)
    * [Using Maven Surefire for POJO Unit Tests](#using-maven-surefire-for-pojo-unit-tests)
    * [Using Maven Surefire for Spock Tests](#using-maven-surefire-for-spock-tests)
    * [Generating Test Reports with Maven](#generating-test-reports-with-maven)
    * [Running Integration Tests with Maven Failsafe](#running-integration-tests-with-maven-failsafe)
    * [Test Coverage with Jacoco](#test-coverage-with-jacoco)
    
Maven Basics
------------

### Maven Coordinates
* Maven Coordinates are used to identify artifacts
* Together bhey identify a 'location' in a Maven repository
    * __groupId__ - Typically unique to an organization
    * __artifactId__ - typically the proejct name
    * __version__ - specific version of the project
* __groupId__ and __version__ can be inherited from a parent POM.

[TOC](#contents)

### Snapshot Versions
* Example: 3.2.1-SNAPSHOT
* Tells Maven this is a development version
* Not stable, Maven should check for newer versions
* Maven will first check locally, then check remote repositories
* By default, Maven will check remote repositories once per day (this option is configurable)

[TOC](#contents)

### Maven Repositories
* Repositories are a location where project artifacts are stored
* Types:
    * Local - userhome/.m2/
    * Central - hosted by Maven community
    * Remote - other locations hosted by companies

[TOC](#contents)

### Dependency Scope
* __Compile__ - Default. Available on all classpaths of project. Also propagated to downstream projects.
* __Provided__ - Like Compile, but expected to be provided by JDK or contatiner at runtime.
* __Runtime__ - Not required for compile, but needed for runtime. On runtime and test classpaths, not compile.
* __Test__ - Only available on test classpath, not transitive.
* __System__ - Similar to provided, but JAR is added to system explicitly. (via file path)
* __Import__ - Imports dependency of POM

[TOC](#contents)

### Maven Standard Directory Layout
```
src
    main
        java
        resources
    test
        java
        resources
```
[Maven guide: introduction-to-the-standard-directory-layout](https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)

[TOC](#contents)

Maven Build Lifecycles
----------------------
* Maven is based on the concept of build lifecycles.
* A lifecycle is pre-defined group of build steps called __phases__.
* Each phase can be bound to one of more plugin __goals__.
* All work done in Maven is done by plugins.
* Lifecycles and phases provide the framework to call plugin goals in a specific sequence.
* Lifecycle is an abstract concept and can't be directly executed. Instead, you execute one or more phases.
* Maven automatically executes all the phases prior to a requested phase.
* The <packaging/> element in pom.xml will automatically assign the right goals for each of the phases.

### How to list available goals of a plugin
`mvn help:describe -Dplugin=compiler`   -- lists goals of compiler plugin

[TOC](#contents)

### Pre-defined lifecycles
* Clean
    * Does a clean of the project, removes all build artifacts from working directory
    * Defined with plugin bindings
* Default
    * Does the build and deployment of your project
    * Does not have any plugin bindings by default. Default build lifecycle defines phases, but the  bindings are going to be defined for each packaging type
* Site
    * Creates a website for your project
    * Defined with plugin bindings
    * Least used in the enterprise
    * See any of the Maven websites for examples, all are build uisng the Maven site lifecycle

[TOC](#contents)

### Clean lifecycle
* Phase: pre-clean --> no binding by default
* Phase: clean --> Plugin binding --> Maven Clean Plugin, Goal: clean
* Phase: post-clean --> no binding by default

[TOC](#contents)

### Default lifecycle

#### Default lifecycle at high-level:
* Validate - Verify project is correct
* Compile - Compile source code
* Test - Test compiled source code
* Package - Package compiled files to packaging type
* Verify - Run Integration Tests
* Install - Install to local Maven repository
* Deploy - Deploy to shared or remote Maven repository

[TOC](#contents)

#### Skipping tests:
`mvn package -Dmaven.test.skip=true`

#### Default lifecycle all phases:
| Phase          | Description          |
|---------------|---------------------------|
| validate      | Validate the project is correct and all necessary information is available    |
| initialize    | Initialize build state, e.g. set properties or create directories             |
| generate-sources  | Generate any source code for inclusion in compilation                     |
| process-sources   | Process the source code, for example to filter any values     |
| generate-resources    | Generate-resources for inclusion in the package           |
| process-resources     | Copy and process the resources into the destination directory, ready for packaging    |
| compile               | Compile the source code of the project    |
| process-classes       | Post-process the generated files from compilation, for example to do bytecode enhancement of Java classes |
| generate-test-sources   | Generate any test source code for inclusion in compilation |
| process-test-sources      | Process the test source code, for example to filter any values    |
| generate-test-resources   | Create resources for testing      |
| process-test-resources    | Copy and process the resources into the test destination directory    |
| test-compile      | Compile the test source code into the test destination directory  |
| process-test-classes  | Post-process the generated files from test compilation, for example to do bytecode enhancement on Java classes |
| test              | Run tests using a suitable unit testing framework. These tests should not require the code be packaged or deployed. |
| prepare-package   | Perform any operations necessary to prepare a package before the actual packaging. This often results in an unpacked, process version of the package. |
| package   | Take the compiled code and package it in its distributable format, such as a JAR or WAR |
| pre-integration-test  | Perform actions required before integration tests are executed. This may involve things such as setting up the required environment. |
| integration-test  | Process and deploy the package if necessary into an environment where integration tests can be run. |
| post-integration-test | Perform actions required after integration tests have been executed. This may include cleaning up the environment. |
| verify    | Run any checks to verify the package is valid and meets quality criteria. |
| install   | Install the package into the local repository, for use as a dependency in other projects locally. |
| deploy    | Done in integration or release environment, copies the final package to the remote repository for sharing with other developers and projects. |

Source:  
[introduction-to-the-lifecycle](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)  
[Lifecycles Reference](https://maven.apache.org/ref/3.8.2/maven-core/lifecycles.html)  
[Plugin Bindings for default Lifecycle Reference](https://maven.apache.org/ref/3.8.2/maven-core/default-bindings.html)

[TOC](#contents)

#### Default Lifecycle - JAR packaging
* Phase: process-resources - Plugin: maven-resources-plugin:resources
* Phase: compile - Plugin: maven-compiler-plugin:compile 
* Phase: process-test-resources - Plugin:maven-resources-plugin:testResources 
* Phase: test-compile - Plugin: maven-compiler-plugin:testCompile
* Phase: test - Plugin: maven-surefire-plugin:test
* Phase: package - Plugin: maven-jar-plugin:jar 
* Phase: install - Plugin: maven-install-plugin:install 
* Phase: deploy - Plugin: maven-deploy-plugin:deploy

[TOC](#contents)

Common Maven Plugins
--------------------
* Clean
* Compiler
* Resources
    * Has 3 goals: resources, testResources, copy-resources
    * Copies project resources to output directory (target dir)
* Surefire
    * Has 1 goal: test
    * Executes unit tests
    * By default includes: Test\*.java; \*Test.java; \*Tests.java; *TestCase.java 
* Jar
    * Has 2 goals: jar, test-jar
* Deploy
* Site

[TOC](#contents)

### Maven Clean Plugin
How to add plugin execution to a specific phase (example):
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-clean-plugin</artifactId>
            <executions>
                <execution>
                    <id>auto-clean</id>
                    <phase>initialize</phase>
                    <goals>
                        <goal>clean</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```
[TOC](#contents)

### Maven Compiler Plugin
* Change bytecode version: `<properties><java.version>1.8</java.version></properties>`
* Display help information on maven-compiler-plugin: `help -Ddetail=true -Dgoal=<goal-name>` to display parameters.

### Other Plugins
* Dependency Analyze: `mvn dependency:analyze`  -- analyzes the dependencies of the project
* Dependency Tree: `mvn dependency:tree`
* Help Describe: `mvn help:describe` -- describes the attributes of a plugin
* Help Effective POM: `mvn help:effective-pom`

[TOC](#contents)

Generating Source with Maven
----------------------------

### XSD to Java
* Create XML Schema (xsd)
* Use `maven-jaxb2-plugin`
* Run `mvn package`
* View generated sources in `target/generated-sources/xjc/` folder
* All compiler classes are packed into JAR file
* Example project: [mavenjaxb](https://github.com/dpopkov/sfg/sfgmaven/mavenjaxb)

[TOC](#contents)

### JSON to Java
* Create JSON Schema
* Use `jsonschema2pojo-maven-plugin`
* Run `mvn package`
* View generated sources in `target/generated-sources/xjc/` folder
* All compiler classes are packed into JAR file
* Example project: [mavenjson](https://github.com/dpopkov/sfg/sfgmaven/mavenjson)
* [jasonschema2pojo Maven Plugin Docs](https://joelittlejohn.github.io/jsonschema2pojo/site/0.5.1/generate-mojo.html)

[TOC](#contents)

### Use MapStruct to generate Java classes
* Add dependency for `mapstruct`
* Configure compiler plugin
* Create mapper using @Mapper annotation
* Example project: [mavenjson](https://github.com/dpopkov/sfg/sfgmaven/mavenmapstruct)
* [MapStruct Documentation](https://mapstruct.org/)

[TOC](#contents)

Alternative JVM Languages
-------------------------

### Compile Groovy with Maven
* Use [groovy-eclipse maven plugin](https://github.com/groovy/groovy-eclipse/wiki/Groovy-Eclipse-Maven-plugin)
* Put Groovy classes into `src/main/groovy`
* Add path to groovy-eclipse-batch jar in Groovy-Eclipse options in IDEA settings
* Example project: [mavengroovy](https://github.com/dpopkov/sfg/sfgmaven/mavengroovy)

[TOC](#contents)

### Compile Kotlin with Maven
* See [Documentation on configuring Maven to support Kotlin](https://kotlinlang.org/docs/maven.html)
* Add Kotlin version and dependency
* Add Kotlin plugin execution into `plugins` section
* Re-configure `maven-compiler-plugin`
* Example project: [mavenkotlin](https://github.com/dpopkov/sfg/sfgmaven/mavenkotlin)

### Compile Scala with Maven
* Can be compiled with Java 8 only

[TOC](#contents)

Testing with Maven
------------------
* __Unit Testing__ is completed by the Maven Surefire Plugin
* __Integration Testing__ is completed by the Maven Failsafe Plugin
* Surefire/Failsafe support:
    * POJO tests
    * JUnit 3-5
    * TestNG
    
[TOC](#contents)

### Using Maven Surefire for POJO Unit Tests
* Do not add any dependencies for JUnit or TestNG
* Create test class and methods beginning with 'test'
* Use plain Java assert(test-condition) 
* Run `mvn test`

[TOC](#contents)

### Using Maven Surefire for Spock Tests

* Add support for Groovy and JUnit 4 ([Example Project Groovy](https://github.com/dpopkov/sfg/sfgmaven/mavengroovy), [Example Project JUnit 4](https://github.com/dpopkov/sfg/sfgmaven/maventestingjunit4))
* Re-configure `maven-surefire-plugin` to use version >= 2.22.0
* Add dependency for Spock with version <= Groovy version
* Add Java class and Spock test (Intellij puts the test into test/java folder, you should move it into test/groovy)
* Add path to groovy-eclipse-batch jar in Groovy-Eclipse options in Java Compiler section of IDEA settings
* Check with `mvn test`
* You may either remove `groovy-all` or add dependency for `surefire-junit-platform` to `maven-surefire-plugin`
* Add support for JUnit 5 (including `junit-jupiter-api`, `junit-jupiter-engine`, `junit-vintage-engine`)
* Add JUnit 5 test
* Check with `mvn test`
* [Example Project Spock](https://github.com/dpopkov/sfg/sfgmaven/mavenspock)

[TOC](#contents)

### Generating Test Reports with Maven
* Add `reporting` section containing `maven-surefire-report-plugin`
* Add `maven-site-plugin` with recent version to `build` section 
* Run `mvn site` phase
* View Surefire Report
* [Example Project](https://github.com/dpopkov/sfg/sfgmaven/mavenspock)

[TOC](#contents)

### Running Integration Tests with Maven Failsafe
* By default, the Failsafe plugin includes test classes with patterns: IT\*.java, \*IT.java, \*ITCase.java
* Test classes can be included or excluded in the plugin `configuration` section
* Add `maven-failsafe-plugin` to `build` section and specify execution goals to hook into phases integration-test and verify
* Calling `mvn test` will run unit tests only 
* Calling `mvn verify` will run both unit and integration tests 
* [Example Project](https://github.com/dpopkov/sfg/sfgmaven/maventestingintegration)

[TOC](#contents)

### Test Coverage with Jacoco
* Read [Creating Code Coverage Reports for Unit and Integration Tests With the JaCoCo Maven Plugin](https://www.petrikainulainen.net/programming/maven/creating-code-coverage-reports-for-unit-and-integration-tests-with-the-jacoco-maven-plugin/)
* Add `jacoco-maven-plugin` to `build, plugins` section
* Add execution for unit tests
* Add execution for integration tests
* Configure `maven-surefire-plugin` to use `surefireArgLine` property
* Configure `maven-failsafe-plugin` to use `failsafeArgLine` property
* Call `mvn verify`
* View reports in `target/site/jacoco-ut/`, `target/site/jacoco-it/`
* [Example Project](https://github.com/dpopkov/sfg/sfgmaven/maventestingintegration)

[TOC](#contents)
