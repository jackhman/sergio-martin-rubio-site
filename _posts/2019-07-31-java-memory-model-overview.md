---
title: Java Memory Model Overview
tags: [Java]
style: fill
color: primary
description: Discover how the JMV manage the memory internally
---

{%- capture list_items -%}
Introduction
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

## Introduction

The JVM uses its own memory model and as soon as it is launch the system allocates memory for the process, and this memory is shared by the 
- **Heap**: Java objects are stored in one of the [heap](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.5.3) areas.
- **[Metaspace](https://www.baeldung.com/java-permgen-metaspace)**: since Java 8 it has replaced the older PermGen memory space to provide a more flexible and reliable memory usage.
- **Stack**: a [JVM stack](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.5.2) is created for each thread and used to store local variables and partial results, perform dynamic linking, return values from methods and dispatch exceptions.
- **[Codecache](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm)**: it is mostly used by the just-in-time (JIT) to store bytecode compiled into native code.

{% include elements/figure.html image="https://lh3.googleusercontent.com/wfKu7pzoAPH-H9VS0w6K2MGn4vVVTU3Q2nM0P-bbl8SlJS6AfFicTDprqv0YGLqA4LpV4elWl7GCBjgNOVkIojcmqmPibGUMzu9Fi2rQ5uAWz1B4GJTALu8gTINW0PtgrH_7AB9sWQ=w800" caption="JVM Memory Model" %}
