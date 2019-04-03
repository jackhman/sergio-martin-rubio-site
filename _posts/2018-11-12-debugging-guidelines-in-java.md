---
title: Debugging Guidelines in Java
tags: [Java, JDK, Debugging, JMeter, JStack]
style: fill
color: warning
description: Debugging is a tool mostly use when an application is not behaving as expected, and every developer should learn how to use it. However, it requires a lot of time to master it.
---

Debugging is a tool mostly use when an application is not behaving as expected, and every developer should learn how to use it. However, it requires a lot of time to master it.

Fortunately, **Java** provides some tools part of **JDK** (jstack...), and there also third party tools, such as [**JMeter**](https://jmeter.apache.org/), that can help us. There is also a very good integration with IDEs such as **IntelliJ** or **Eclipse**, so it is very important to know how to use the debugging features of our favourite IDE.

In order to speed up this learning process we can follow these guidelines:

1. Do not be afraid of reading code, specially if the code is from others. We should go as deep as we can to understand what is really happening under the hood.
2. Do not blame **JDK** or libraries. Never assume that **Java** is broken, because probabilities are always against you. 99% of the times the bug is on something you coded.
3. Make assumptions, but do not trust yourself, because we might create blind spots.
4. Walk away from the problem and come back when you are fresh (work out, go to bed, meet your friends…).
5. Do not program by coincidence, or in other words, do not try stuff without knowing what you are doing.
6. Google is your best coding friend.
7. Check JDK [javadoc](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/javadoc.html) when you are not sure about how a **Java** feature works.
8. Use proper log levels (info, warn, error, debug).
9. Use breakpoints to have a full view of the program on a particular point of our application. Conditions on breakpoints are also very useful when we are debugging a loop with thousands of values.
10. Use tools such as **JMeter** for load testing or [jstack](https://docs.oracle.com/javase/7/docs/technotes/tools/share/jstack.html) to capture thread dumps.
11. Have a remote debugger for your applications deployed on dev and pre-production environments.
12. If we are not able to find the source of the issue by any of the previous guidelines; firstly, start eliminating code; secondly, check your environment; and thirdly, check libraries.

Debugging is an art hard to master, and will definitely require a few years of experience, so start building your debugging skills as soon as possible!