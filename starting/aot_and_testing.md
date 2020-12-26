---
title: AOT compilation and testing
parent: Getting started
has_children: true
nav_order: 1
published: true
---

# Introduction

Unit testing of any system is critical. The previous example creates the Fluxtion processor at runtime, but applies no tests. Fluxtion provides two sets of tools that make testing at build time easier.
1.  Junit utilities for generating and testing event processors 
1.  A maven plugin that generates the processor ahead of time

## Development process
Integrating unit testing into the developer workflow with the following steps:
1.  Create a test class that extends the [BaseSeInprocessTest.java](https://github.com/v12technology/fluxtion/blob/2.10.9/generator/src/test/java/com/fluxtion/generator/util/BaseSepInprocessTest.java)
1.  Write a test that creates the event processor. Fire events into the geneated processor and validate outputs or state of nodes
1.  Add fluxtion plugin to generate the event processor at build time. 
1.  Update tests to use the build time generated event processor 

### First unit test
Add the fluxtion test jar to the project. Maven test-scoped dependemcies are not transitive so both artefacts are explicitly declared.


```xml
<dependency>
	<groupId>com.fluxtion</groupId>
    <artifactId>generator</artifactId>
    <type>test-jar</type>
    <scope>test</scope>
    <version>${fluxtion.ver}</version>
</dependency>
<dependency>
	<groupId>com.fluxtion.extension</groupId>
    <artifactId>fluxtion-streaming-builder</artifactId>
    <type>test-jar</type>
    <scope>test</scope>
    <version>${fluxtion.ver}</version>
</dependency>
```

