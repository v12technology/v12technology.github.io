---
title: Unit testing
parent: Getting started
has_children: true
nav_order: 1
published: true
---

# Introduction

Unit testing of any system is critical. The previous example creates an event processor at runtime, but applies no tests. This example demonstrates how to write unit tests that validate event processing logic. Fluxtion provides Junit utilities for generating and testing event processors.

## Testing process
Fluxtion integrates unit testing into the developer workflow as follows:
1. Add maven dependencies for testing and create a test class that extends the [BaseSeInprocessTest.java](https://github.com/v12technology/fluxtion/blob/2.10.9/generator/src/test/java/com/fluxtion/generator/util/BaseSepInprocessTest.java).
1. Extract processor construction into a separate builder method for use in a unit test.   
1. Write a test case that creates the event processor using the builder method within the test. Fire events into the geneated processor and validate outputs or state of nodes using asserts/expectations. 

### 1. Maven dependencies
Add the fluxtion test jar to the project and Junit 4 dependency.

```xml
<dependency>
  <groupId>com.fluxtion</groupId>
  <artifactId>generator</artifactId>
  <type>test-jar</type>
  <scope>test</scope>
  <version>${project.version}</version>
</dependency>
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.13.1</version>
  <scope>test</scope>
</dependency>
```

### 2. Introduce builder method
The app is refactored to separate consruction logic into a builder method. To help testing a node can be given unique identifier by appending  `.id("name")` during construction. The BaseSepInprocessTest provides helper methods to access a node in the event processor using the id in a test.
```java
public class TradeMonitor {
    
    public static void main(String[] args) throws Exception {
        publishTestData(reuseOrBuild(TradeMonitor::build));
    }

    public static void build(SEPConfig cfg) {
        groupBySum(Trade::getSymbol, Trade::getAmount)
            .sliding(seconds(1), 5)
            .comparator(numberValComparator()).reverse()
            .top(3).id("top3")
            .map(TradeMonitor::formatTradeList)
            .log();
    }
  ...
  }
```
