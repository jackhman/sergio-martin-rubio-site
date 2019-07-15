---
title: Structural Patterns Features
tags: [Java, Design Pattern]
style: fill
color: primary
description: Structural design patterns reduce complexity in the OOP design by identifying a ways to create relationships between entities
---

{%- capture list_items -%}
Structural Patterns Features
Proxy Pattern
Adapter Pattern
Decorator Pattern
Bridge Pattern
Choose the Right Pattern
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

## Structural Patterns Features

 Name | Intent | Motivation | Applicability | Consequences | Implementation
---------|----------|---------|----------|---------|----------|---------
**Proxy** | It encapsulates an object to control references to it | Proxy pattern is usually used to control access of resources. For instance, if you only need to use some methods at a particular moment, there is no point in instantiating an initializing a costly object until the resources are needed | Considering the following example which is base on a calculator of prime numbers, we can see that there is no need to do the heavy computation until getNumberOfPrimes is called, therefore the proxy pattern could be a solution to lazily perform the computation when the data is actually needed | This pattern hides the original class behaviour to the client. Therefore, the risk of using this pattern it is that you might not be aware of what is happening behind the scenes, and code could be inefficient |
**Adapter** | This pattern wraps an interface of a class into another interface that is required by the client. so it works as a bridge between to classes | It used when a specific interface is required by the client | This pattern can be apply when the target interface and the one required by the client are very similar | The adapter class should do only the necessary to adapt the original class, so no additional logic should be added to the adapter | In case that the interface required by the client and the Adaptee are very different, it will require further modifications in order to make them compatible
**Decorator** | The main purpose of the Decorator Pattern is to add additional features, behaviours or responsibilities dynamically to an object | This pattern is a great alternative to subclassing, because instead of using inheritance to provide new functionalities, the decorator pattern wraps the target object with decorator classes to add the new behaviours | It is used when functionalities or responsibilities need to be added dynamically to a class, because otherwise many subclasses need to be created to get the same results | This pattern allows you to add or remove functionalities dynamically and avoids producing an explosion of subclasses to support every combination | Keep in mind that many decorators can be hard to maintain and distinguish and debugging is harder, since the behaviour of the object changes at runtime
**Bridge** | It decouples abstraction from implementation and avoids class explosion | This pattern give you the freedom of developing abstraction and implementation independently and create multiple combinations. The client only have access to the abstraction and does not need to know anything about the implementation | This pattern can be use when we want to decouple abstraction and implementation because they change independently, and we want to avoid a class explosion | Abstraction and implementation can be extended independently, so increases the loose coupling between class abstraction and its implementation. On the other hand, bridge pattern increases complexity due to the fact that it uses composition over inheritance | When using this pattern make sure you are grouping related abstractions and implementation to use the same abstract class or interface

## Proxy Pattern

{% include elements/figure.html image="https://lh3.googleusercontent.com/kxocuD48AXcfJ0Fj-VuM9_AAJCsEQhMrXM1orNdEUgwqGeZMEoo7utS6eX7bZGhJ0S070bri6xmJ5Hine0bkZQVrVPGMmIZuUoAmFEuSSEMZrRKzSJIoA1D9gRDNs0sO1l6LhTj6PA=w2400" caption="Proxy Pattern - UML" %}

### Use Cases

- An expensive computation needs to be performed.
- A network call is necessary.

<p class="text-center">
{% include elements/button.html link="https://github.com/smartinrub/primes-proxypattern" text="Source Code" %}
</p>

## Adapter Pattern

{% include elements/figure.html image="https://lh3.googleusercontent.com/vZA2TM2eVe2R6KBc022BYmGFB5D09ktaytHFJnDUTLPfblNGUgyOZs8qHN9voJAbsukihubjZypPiGSivlswyz4AskDIGGQkU6hIksAswN8PRaPQVB4_yDeXsFdDVcm56L9Wqmvtzw=w2400" caption="Adapter Pattern - UML" %}

### Use Cases

- It is commonly used to adapt third party libraries and frameworks.

<p class="text-center">
{% include elements/button.html link="https://github.com/smartinrub/java-adapter-pattern" text="Source Code" %}
</p>

## Decorator Pattern

{% include elements/figure.html image="https://lh3.googleusercontent.com/ltO5XFAoBiJCieliVbbkPjFTXBk2POGgKz0fz0KP236sbWeZl1t_b3TsXyazmFSESgdQdqe8bZ9YT8loHFut9vCS3H8iyuMZ4ffCVp0d_Z4Q_Bbj3AiISRkPl7A9jFgDB0r36sUJPg=w2400" caption="Decorator Pattern - UML" %}

### Use Cases

- The Java I/O library makes use of the Decorator Pattern

```java
BufferedInputStream bis = new BufferedInputStream(new FileInputStream(new File("a.txt")));
while(bis.available() > 0){
        char c = (char)bis.read();
        System.out.println("Char: " + c);
}
```

<p class="text-center">
{% include elements/button.html link="https://github.com/smartinrub/java-decorator-pattern" text="Source Code" %}
</p>

## Bridge Pattern

{% include elements/figure.html image="https://lh3.googleusercontent.com/T1SOpKGNRgTcSoIy737rYAbJ6nSGqEoPfCBzdy0RIvmBJYMOpa1vYOi-6Zm9uaioFAGiejVz726pxa2RCox7m67fWCC3k8RQAD6UfY8E2ZYTLPqhA5zPI-ESk9NZVmI2e8vjK2YcUQ=w2400" caption="Bridge Pattern - UML" %}

<p class="text-center">
{% include elements/button.html link="https://github.com/smartinrub/java-bridge-pattern" text="Source Code" %}
</p>

## Choose the Right Pattern

The following table shows the main five structural patterns with definitions from “Head First Design Patterns” and “Design Patterns: Elements of Reusable Object-Oriented Softwar“.

Pattern | Definition | Technique | When to use
---------|----------|---------|---------
 **Decorator** | Attaches additional responsibilities to an object dynamically | Uses composition instead of inheritance to combine classes | Decouple behaviours and compose them in different ways
 **Facade** | Provides an unified interface to a set of interfaces in a subsystem | Wraps a complex system | Provide a higher level interface that interacts with a complex subsystem
 **Adapter** | Converts the interface of a class into another interface that the client expects | Wraps an object inside another object | Adapt one interface to another interface
 **Proxy** | Provides a place holder for another object in order to control access to it | Proxy and target object share same interface | Control access to another object
 **Bridge** | Decouples an abstraction from its implementation | Abstraction and implementation implement different interfaces | Combine two ways of doing something

As you can see, these patterns are very similar but they solve different problems, so it is important to make sure you use the right one in each architectural problem you want to solve.


