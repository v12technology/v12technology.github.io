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
1. Add the Fluxtion maven plugin to the build
1. Annotate any Fluxtion builder methods with `@SepBuilder` providing the fully qualified name of the generated processor as a parameter
1. Remove any calls to dynamically build and use the fqn above to instantiate a processor
