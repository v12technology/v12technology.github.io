---
title: Getting started
has_children: true
nav_order: 2
---

# Introduction

This section covers getting an example up and running with Fluxtion. The goal is to complete an inital application within 5 minutes and observe realtime event processing. A developer must be competent with Java and maven to complete this tutorial.

# Example
Our goal is to listen to a stream of trade events and every second prints out the three most active stocks for the last five seconds. 
## Development process
To build a Fluxtion application requires three steps
1. Create a maven project including fluxtion plugin and dependencies. 
1. Write processing logic using Fluxtion api's. Create tests to validate the logic
1. Integrate fluxtion generated processor into application

