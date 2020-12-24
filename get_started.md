---
title: Getting started
has_children: true
nav_order: 2
published: true
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
            <groupId>com.fluxtion.extension</groupId>
            <artifactId>fluxtion-streaming-builder</artifactId>
            <version>${fluxtion.ver}</version>
        </dependency>
    </dependencies>
</project>
```

### Fluxtion processing
