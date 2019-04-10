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
{%- endcapture -%}

{% include list.html title="Content" type="toc" %}

## Structural Patterns Features

 Name | Intent | Motivation | Applicability | Consequences | Implementation
---------|----------|---------|----------|---------|----------|---------
 **Proxy** | It encapsulates an object to control references to it | Proxy pattern is usually used to control access of resources. For instance, if you only need to use some methods at a particular moment, there is no point in instantiating an initializing a costly object until the resources are needed | Considering the following example which is base on a calculator of prime numbers, we can see that there is no need to do the heavy computation until getNumberOfPrimes is called, therefore the proxy pattern could be a solution to lazily perform the computation when the data is actually needed | This pattern hides the original class behaviour to the client. Therefore, the risk of using this pattern it is that you might not be aware of what is happening behind the scenes, and code could be inefficient |
 **Adapter** | This pattern wraps an interface of a class into another interface that is required by the client. so it works as a bridge between to classes | It used when a specific interface is required by the client | This pattern can be apply when the target interface and the one required by the client are very similar | The adapter class should do only the necessary to adapt the original class, so no additional logic should be added to the adapter | In case that the interface required by the client and the Adaptee are very different, it will require further modifications in order to make them compatible
  **Decorator** | The main purpose of the Decorator Pattern is to add additional features, behaviours or responsibilities dynamically to an object | This pattern is a great alternative to subclassing, because instead of using inheritance to provide new functionalities, the decorator pattern wraps the target object with decorator classes to add the new behaviours | It is used when functionalities or responsibilities need to be added dynamically to a class, because otherwise many subclasses need to be created to get the same results | This pattern allows you to add or remove functionalities dynamically and avoids producing an explosion of subclasses to support every combination | Keep in mind that many decorators can be hard to maintain and distinguish and debugging is harder, since the behaviour of the object changes at runtime

## Proxy Pattern

{% include figure.html image="" caption="Proxy Pattern - UML" %}

### Use Cases

- An expensive computation needs to be performed.
- A network call is necessary.

<p class="text-center">
{% include button.html link="https://github.com/smartinrub/primes-proxypattern" text="Source Code" %}
</p>

## Adapter Pattern

{% include figure.html image="" caption="Adapter Pattern - UML" %}

### Use Cases

- It is commonly used to adapt third party libraries and frameworks.

<p class="text-center">
{% include button.html link="https://github.com/smartinrub/java-adapter-pattern" text="Source Code" %}
</p>

## Decorator Pattern

{% include figure.html image="" caption="Decorator Pattern - UML" %}

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
{% include button.html link="https://github.com/smartinrub/java-decorator-pattern" text="Source Code" %}
</p>
