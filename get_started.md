---
title: Getting started
has_children: true
nav_order: 2
published: true
---
# Introduction
This example processes a stream of trade events, every second printing out the three most active stocks for the previous five seconds. An understanding of Java and maven is required, the example is located [here](https://github.com/v12technology/fluxtion/tree/2.10.11/examples/quickstart/lesson-1).
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
public static void main(String[] args) throws Exception {
  StaticEventProcessor processor = reuseOrBuild(c -> {
    groupBySum(Trade::getSymbol, Trade::getAmount)
      .sliding(seconds(1), 5)
      .comparator(numberValComparator()).reverse()
      .top(3)
      .map(TradeMonitor::formatTradeList)
      .log();
  });
  TradeGenerator.publishTestData(processor);
}

public static String formatTradeList(List<Tuple<String, Number>> trades) {
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

- Line 2 Creates an event processor if one cannot be found on the class path for this builder
- Line 3 Creates an aggregate sum of the trade amount, grouped by symbol name
- Line 4 Defines a sliding window, publishing every second with a total window size of 5 seconds
- Line 5 Applies a comparator function to the cumulative sum and then reverses the sort order
- Line 6 Filters the list of trades to the top 3 by volume
- Line 7 Applies a mapping function to pretty print the filtered list of trades
- Line 8 Logs the pretty print function on every bucket expiry after first 

### 3. Application integration

An application must feed events into a Fluxtion generated event processor for processing. All Fluxtion event processors implement the [StaticEventProcessor](https://github.com/v12technology/fluxtion/blob/develop/api/src/main/java/com/fluxtion/api/StaticEventProcessor.java). The application instantiates the event processor and invokes `processor.onEvent(event)` to post an event

```java
public class TradeGenerator {

    private static final String[] ccyPairs = new String[]{"EURUSD", "EURCHF", "EURGBP", "GBPUSD",
        "USDCHF", "EURJPY", "USDJPY", "USDMXN", "GBPCHF", "EURNOK", "EURSEK"};

    static void publishTestData(StaticEventProcessor processor) throws InterruptedException {
        Random random = new Random();
        while (true) {
            processor.onEvent(new TradeMonitor.Trade(ccyPairs[random.nextInt(ccyPairs.length)], random.nextInt(100) + 10));
            Thread.sleep(random.nextInt(10) + 10);
        }
    }

}
```
The utility method above generates random currency pair trade events and posts them to the supplied event processor.
## Running the application

Running the application will generate the event processor and after about 5 seconds, the following will be output to the console:

```console
 Most active ccy pairs in past 5 seconds:
	 1. EURGBP - 2390 trades
	 2. USDMXN - 2164 trades
	 3. USDJPY - 1921 trades
 Most active ccy pairs in past 5 seconds:
	 1. USDMXN - 2447 trades
	 2. EURUSD - 1987 trades
	 3. EURGBP - 1913 trades
 Most active ccy pairs in past 5 seconds:
	 1. USDMXN - 2262 trades
	 2. EURGBP - 2018 trades
	 3. EURUSD - 2002 trades
``` 
