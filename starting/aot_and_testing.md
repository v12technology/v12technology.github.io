---
title: Unit testing
parent: Getting started
has_children: true
nav_order: 1
published: true
---

# Introduction

Unit testing of any system is critical. The previous example creates the Fluxtion processor at runtime, but applies no tests. Fluxtion provides two tools to simplify testing:
1.  Junit utilities for generating and testing event processors.
1.  A maven plugin that generates an event processor as part of the build. 

## Development process
Fluxtion integrates unit testing into the developer workflow as follows:
1.  Create a test class that extends the [BaseSeInprocessTest.java](https://github.com/v12technology/fluxtion/blob/2.10.9/generator/src/test/java/com/fluxtion/generator/util/BaseSepInprocessTest.java).  Write a test case that creates the event processor using one of the sep parent methods.
1.  Fire events into the geneated processor and validate outputs or state of nodes using asserts/expectations.
1.  Add fluxtion maven plugin to generate the event processor at build time. 
1.  Update tests to use the build time generated event processor 

### First unit test
Add the fluxtion test jar to the project. Maven test-scoped dependencies are not transitive so both artefacts are explicitly declared.

```xml
<dependencies>
    <dependency>
        <groupId>com.fluxtion</groupId>
        <artifactId>generator</artifactId> 
        <type>test-jar</type>
        <scope>test</scope>
        <version>${fluxtion.ver}</version>
    </dependency>
    <dependency>
        <groupId>com.fluxtion.extension</groupId> The unit test can
        <artifactId>fluxtion-streaming-builder</artifactId>
        <type>test-jar</type>
        <scope>test</scope>
        <version>${fluxtion.ver}</version>
    </dependency>
</dependencies>
```

The unit test can reference the generated event processor within the test case, pushing events and accessing nodes via an id. The id of the node is assigned during the build process automatically, this can he overriden by the developer with .id("name") at build time.
