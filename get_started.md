---
title: Getting started
has_children: true
nav_order: 2
---

# Introduction
Using java and maven the goal is to listen to a stream of trade events and every second prints out the three most active stocks for the last five seconds. 
## Development process
To build a Fluxtion application requires three steps
1. Create a maven project including fluxtion plugin and dependencies. 
1. Write processing logic using Fluxtion api's. Create tests to validate the logic
1. Integrate fluxtion generated processor into application
### Maven build
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.fluxtion.articles</groupId>
    <artifactId>crosscalc</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>15</maven.compiler.source>
        <maven.compiler.target>15</maven.compiler.target>
        <fluxtion.ver>2.10.3</fluxtion.ver>
    </properties>
    
    <profiles>
        <profile>
            <id>fluxtion-generate</id>
            <properties>
                <skipTests>true</skipTests>
            </properties>
            <activation>
                <activeByDefault>false</activeByDefault>
            </activation>
            <build>
                <plugins>
                    <plugin>
                        <groupId>com.fluxtion</groupId>
                        <artifactId>fluxtion-maven-plugin</artifactId>
                        <version>${fluxtion.ver}</version>
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


    <dependencies>
        <dependency>
            <groupId>com.fluxtion.extension</groupId>
            <artifactId>fluxtion-text-api</artifactId>
            <version>${fluxtion.ver}</version>
        </dependency>
        <dependency>
            <groupId>com.fluxtion.extension</groupId>
            <artifactId>fluxtion-text-builder</artifactId>
            <scope>provided</scope>
            <version>${fluxtion.ver}</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.12</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <scope>test</scope>
            <version>4.12</version>
        </dependency>
    </dependencies>
</project>
```
