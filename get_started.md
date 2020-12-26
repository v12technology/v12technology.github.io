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
### Maven build

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.fluxtion.tutorial</groupId>
    <artifactId>quickstart</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>15</maven.compiler.source>
        <maven.compiler.target>15</maven.compiler.target>
        <fluxtion.ver>2.10.3</fluxtion.ver>
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
              <version>${fluxtion.ver}</version>
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

### Fluxtion strem processing logic
Define the procesing using Fluxtin streaming api. 

```java
public static void build(SEPConfig cfg) {
    groupBySum(Trade::getSymbol, Trade::getAmount)
            .sliding(seconds(1), 5)
            .comparator(numberValComparator()).reverse()
            .top(3)
            .log();
}

@Data
public static class Trade {
    private String symbol;
    private double amount;
}
```

- Line 2 creates an aggregate sum of the trade amount, grouped by symbol name. 
- Line 3 defines a sliding window, publishing every second with a total window size of 5 seconds
- Line 4 applies a comparator function to the cumulative sum sort and then reverses the sort order.
- Line 5 Filters the top 3 trades by volume
- Line 6 logs the result of top every publish

### Intgerate into Application

Fluxtion provides a pipeline abstraction to feed events from a source into an event processor. in this case a manually injetcing event source is used to feed Trade events into the pipeine. The first processing stage is the event processor defined above.

```java
public static void main(String[] args) throws Exception {
    ManualEventSource<Trade> tradeSource = new ManualEventSource<>("trade-source");
    flow(tradeSource)
            .first(SepStage.generate(TradeMonitor::build))
            .start();

    Random random = new Random();
    while (true) {
        tradeSource.publishToFlow(new Trade(ccyPairs[random.nextInt(ccyPairs.length)], random.nextInt(100) + 10));
        Thread.sleep(random.nextInt(10) + 10);
    }
}

private static final String[] ccyPairs = new String[]{
    "EURUSD", "EURCHF", "EURGBP", "GBPUSD", "USDCHF", "EURJPY", 
    "USDJPY", "USDMXN", "GBPCHF", "EURNOK", "EURSEK"
};

```
