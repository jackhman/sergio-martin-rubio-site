---
title: Java Memory Model Overview
tags: [Java]
style: fill
color: primary
description: Discover how the JMV manage the memory internally
---

{%- capture list_items -%}
Introduction
Heap Memory
Young Generation
Old Generation
Generational Garbage Collection Process
Garbage Collector Selection
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

The Old Generation is used to store long surviving objects. To clasify an object as a candidate to be moved from the young generation to the old generation a threshold is set. Eventually the old generation needs to be collected and this event is called a major garbage collection.

A mayor garbage collection is also a _Stop the World_ event. However, this type of Stop the World is much slower since it involves live objects, so this kind of events should be minimized.

### Generational Garbage Collection Process

The Garbage Collector is an automatic process reponsible for identifying and delete unsed objects. Steps:

1. New objects are put in the eden and survivor spaces start empty.
2. When the eden space is full a minor garbage collection is triggered. Referenced objects are moved to the first survivor space (S0) and unreferenced objects are removed.
3. Next time the eden fills up it will happen the same, but this time referenced objects are moved to the second survivor space (S1), and referenced objects from S0 are also moved to S1 and age is incremented. Unreferenced object in S0 are removed.
4. During the next minor GC, the same process occurs, but this time survivor spaces switch. Referenced objects are moved to S0, survivors are aged and unreferenced objects from eden and S1 are removed.
5. When objects reach a particular age they are promoted from the young generation to the old generation.
6. The previous 5 steps repeat until a major GC is performed on the old generation which cleans up and compact the space.

{% include elements/figure.html image="https://lh3.googleusercontent.com/5li9Ai49SfxisvdcKlUS3s3ryvNSn7yDeGq4CsLC3dORyuumzV3ih41jXfNr0Q-gH_tLIc58as8CLybPTng8D7Uar1cCRN4r4lD8jDTfmP2YBIVRcsbmkzhcriQ0tnA1pJO1-xqEnQ=w800" caption="Generational Garbage Collection Process Diagram" %}

### Garbage Collector Selection

When no Garbage Collector is selected by CLI (e.g. `-XX:+UseSerialGC`), the JVM selects a GC base on the platform is running on, heap size and runtime compiler. The process of selecting a particular GC is called **[Ergonomics](https://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/ergonomics.html#ergonomics)**.

[GC Types](https://javapapers.com/java/types-of-java-garbage-collectors/):

1. _Serial Garbage Collector_ - S GC: It uses a single thread for garbage collection and freezes all the threads while doing the garbage collection.
2. _Parallel Garbage Collector_ - P GC: It uses multiple threads and also freezes all the threads during garbabe collection.
3. _CMS (Concurrent Mark Sweep) Garbage Collector_ - CMS GC: It uses multiple threads and only freezes the threads while marking the referenced objects in the tenured generation space, and if there is a change in heap memory in parallel while doing the garbage collection. It uses more CPU to improve throughput.
4. _[G1 Garbage Collector](https://www.oracle.com/technetwork/tutorials/tutorials-1876574.html)_ - G1 GC: It is used for multi-processor machines with large memories. It supposed to replace CMS in the long term and the main differences with CMS are:
- It uses regions to simplify the collection and avoid fragmentation issues.
- G1 offers more predictable garbage collection pauses and allows users to specify desired pause targets.

{% include elements/figure.html image="https://lh3.googleusercontent.com/wZSqu7r16KpwA2-7pbQFzorYharzcuhnLi5R21xcYpzQ2zf0F_D3HOE7aUP_sRo3TNWT6nf3XzZx5JWT0-nTnsLBubzp21BnJfJDno5fj1lzkS12CaJ9a4CKtp4l7XMxlPRPqQ9JFg=w800" caption="Generational Garbage Collection Process Diagram" %}

5. _[The Z Garbage Collector](https://www.opsian.com/blog/javas-new-zgc-is-very-exciting/)_ - ZGC: Is designed to offer very low pause times on large heaps and it does this through the use of coloured pointers and load barriers.

The GC selected by default in your machine can be checked with:

```shell
java -XX:+PrintCommandLineFlags -version
```

If a parallel GC is selected we can tune a couple of parameters:

- _Maximum Pause Time Goal_ (`-XX:MaxGCPauseMillis=<milliseconds>`): The JVM will ajust the heap size and other GC parameters to reduce the pause time during garbage collector stops. This might cause more frequent GC and reduce the application throughput.
- _Throughput Goal_ (`-XX:GCTimeRatio=<milliseconds>`): The ratio is `1 / (1 + <milliseconds>)`. For instance, if it is set to 19, it will be 1/20th or 5% of the total time for the garbage collection. This ratio affects both the young and old generation time. The JVM might increase the size of the generations, so the application will run for longer without having to execute a garbage collection.

> Oracle docummentation advices to not choose a maximum value for the heap unless you know that you need a heap greater than the default maximum heap size. Instead choose a throughput goal that is sufficient for your application.

