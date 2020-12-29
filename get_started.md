---
title: Getting started
has_children: true
nav_order: 2
published: true
---
# Introduction
This example processes a stream of trade events, every second printing out the three most active stocks for the previous five seconds. 
## Development process
Building a Fluxtion application requires three steps
1. Create a maven project with the required dependencies. 
1. Write processing logic using Fluxtion api's. 
1. Integrate fluxtion generated processor into an application

### 1. Maven build

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.fluxtion.example</groupId>
    <artifactId>quickstart.lesson-1</artifactId>
    <version>2.10.10-SNAPSHOT</version>
    <packaging>jar</packaging>
    <name>fluxtion :: quickstart :: lesson-1</name>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.12</version>
        </dependency>
        <dependency>
            <groupId>com.fluxtion.integration</groupId>
            <artifactId>fluxtion-integration</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-api</artifactId>
            <version>2.13.3</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>2.13.3</version>
        </dependency>
    </dependencies>
</project>
```

### 2. Fluxtion stream processing logic
Define the procesing using Fluxtin streaming api. 

```java
public static void build(SEPConfig cfg) {
  groupBySum(Trade::getSymbol, Trade::getAmount)
    .sliding(seconds(1), 5)
    .comparator(numberValComparator()).reverse()
    .top(3)
    .map(TradeMonitor::formatTradeList)
    .log();
}

private static String formatTradeList(List<Tuple<String, Number>> trades){
  StringBuilder sb = new StringBuilder("Most active ccy pairs in past 5 seconds:");
  for (int i = 0; i < trades.size(); i++) {
    Tuple<String, Number> result = trades.get(i);
    sb.append(String.format("\n\t%2d. %5s - %d trades", i + 1, result.getKey(), result.getValue().intValue()));
  }
  return sb.toString();
}

@Data
@AllArgsConstructor
@NoArgsConstructor
public static class Trade {

  private String symbol;
  private double amount;

}
```

- Line 2 creates an aggregate sum of the trade amount, grouped by symbol name
- Line 3 defines a sliding window, publishing every second with a total window size of 5 seconds
- Line 4 applies a comparator function to the cumulative sum and then reverses the sort order
- Line 5 Filters the list of trades to the top 3 by volume
- Line 6 logs the result of filtered list every publish

### 3. Application integration

Fluxtion provides a pipeline abstraction to feed events from a source into an event processor. In this case a manually injecting event source is used to feed Trade events into the pipeine.

```java
public static void main(String[] args) throws Exception {
  ManualEventSource<Trade> tradeInjector = new ManualEventSource<>("trade-source");
  flow(tradeInjector)
    .sep(TradeMonitor::build)
    .start();
  TradeGenerator.publishTestData(tradeInjector);
}
```
-  line 1 creates a trade injector that pushes events into a pipeline programmatically
-  line 3 initialises a flow with the trade injector as an event source
-  line 4 adds fluxtion event processor as a pipeline stage
-  line 5 starts the pipeline, builds the processing logic during initialisation phase
-  line 7-17 generate random trade events and publish to pipeline for processing
