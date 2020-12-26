---
title: AOT compilation and testing
parent: Getting started
has_children: true
nav_order: 1
published: true
---

# Introduction

Unit testing of any system is critical. The previous example creates the Fluxtion processor at runtime, but applies no tests. Fluxtion provides two sets of tools that make testing at build time easier.
-  Junit utilities for generating and testing event processors 
-  A maven plugin that generates the processor ahead of time
## Testing
Fluxtion provides a [utility class](https://github.com/v12technology/fluxtion/blob/2.10.9/generator/src/test/java/com/fluxtion/generator/util/BaseSepInprocessTest.java "BaseSeInprocessTest") that simplifies unit testing for event processors  
