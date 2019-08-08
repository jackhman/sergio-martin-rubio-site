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

The JVM uses its own memory model and as soon as it is launch the system allocates memory for the process, and this memory is shared by:
- **Heap Memory**: Java objects are stored in one of the [heap](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.5.3) areas.
- **[Metaspace](https://www.baeldung.com/java-permgen-metaspace)**: since Java 8 it has replaced the older PermGen memory space to provide a more flexible and reliable memory usage.
- **Stack Memory**: a [JVM stack](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.5.2) is created for each thread and used to store local variables and partial results, perform dynamic linking, return values from methods and dispatch exceptions.
- **[Codecache](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm)**: it is mostly used by the just-in-time (JIT) to store bytecode compiled into native code.

{% include elements/figure.html image="https://lh3.googleusercontent.com/wfKu7pzoAPH-H9VS0w6K2MGn4vVVTU3Q2nM0P-bbl8SlJS6AfFicTDprqv0YGLqA4LpV4elWl7GCBjgNOVkIojcmqmPibGUMzu9Fi2rQ5uAWz1B4GJTALu8gTINW0PtgrH_7AB9sWQ=w800" caption="JVM Memory Model" %}

## Heap Memory

The heap memory is divided into two areas or generations, young (also called nursery) and old generation.  The heap is where your object data is stored and it is managed by the [Garbage Collector](https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html).

>Garbage collection: is the process of looking at the heap memory to clean up the unused objects. An Used object is a referenced object, which means that  somewhere in your program there is something pointing to that object. In other languages like C this is a manual process whereas in Java is handled automatically.

### Young Generation

If the young generation fills up, this causes a minor garbage collection and it triggers a _Stop the World_. Surviving objects are moved to the old generation.

>_Stop the World_: All the application threads are stoped until a particular operation finishes.

Inside the Young Generation there are three areas: Eden, Survivor Space 0, and Survivor Space 1.

### Old Generation

The Old Generation is used to store long surviving objects. To clasify an object as a candidate to be moved from the young generation to the old generation a threshold is set. Eventually the old generation needs to be collected. This event is called a major garbage collection.

A mayor garbage collection is also a Stop the World event. However, this type of Stop the World is much slower since it involves live objects, so this kind of events should be minimized.