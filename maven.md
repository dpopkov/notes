Maven
=====
Contents
--------
* [Maven Build Lifecycles](#maven-build-lifecycles)
    * [How to list available goals of a plugin](#how-to-list-available-goals-of-a-plugin)
    * [Pre-defined lifecycles](#pre-defined-lifecycles)
    * [Clean lifecycle](#clean-lifecycle)
    * [Default lifecycle](#default-lifecycle)
        * [Default lifecycle at high-level](#default-lifecycle-at-high-level)
        * [Skipping tests](#skipping-tests)
        * [Default lifecycle all phases](#default-lifecycle-all-phases)
        * [Default Lifecycle - JAR packaging](#default-lifecycle---jar-packaging)

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

Source: [introduction-to-the-lifecycle](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)

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