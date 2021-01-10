---
title: Buildtime generation
parent: Getting started
has_children: false
nav_order: 2
published: true
---

# Introduction
Fluxtion provides a maven plugin that can generate an event processor as part of the build cycle. This makes a system more predictable at runtime as the behaviour is statically generated before deployment.

## Development process
To statically generate the event processor at buildtime three steps are required
1. Add the Fluxtion maven plugin to the build.
1. Annotate any Fluxtion builder methods with `@SepBuilder` providing the fully qualified name of the generated processor as a parameter.
1. Remove any calls to dynamically build a processor at runtime and use the fqn above to instantiate a statically generated processor, including test cases.

### 1. Maven Build
Add the fluxtion maven plugin using the scan goal. It is usally better to add the generation as a maven profile as once the solution is generated then the build should be stable. Skipping tests for the generation phase is usually preferable.

```xml
    <profiles>
        <profile>
            <id>fluxtion-generate</id>
            <properties>
                <skipTests>true</skipTests>
            </properties>
            <build>
                <plugins>
                    <plugin>
                        <groupId>com.fluxtion</groupId>
                        <artifactId>fluxtion-maven-plugin</artifactId>
                        <version>${project.version}</version>
                        <executions>
                            <execution>
                                <goals>
                                    <goal>scan</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
        </profile>
    </profiles>
```

### 2. Annotate builder methods
The scan goal searches the project code base for any builder methods that have the `@SepBuilder` annotation. 

```java
    @SepBuilder(name = "TradeEventProcessor", packageName = "com.fluxtion.example.quickstart.lesson3.generated")
    public static void build(SEPConfig cfg) {
        groupBySum(TradeMonitor.Trade::getSymbol, TradeMonitor.Trade::getAmount)
            .sliding(seconds(1), 5)
            .comparator(numberValComparator()).reverse()
            .top(3).id("top3")
            .map(TradeMonitor::formatTradeList)
            .log();
    }
```

