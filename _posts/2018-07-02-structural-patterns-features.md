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
{%- endcapture -%}

{% include list.html title="Content" type="toc" %}

## Structural Patterns Features

 Name | Intent | Motivation | Applicability | Consequences | Implementation
---------|----------|---------|----------|---------|----------|---------
 **Proxy** | It encapsulates an object to control references to it | Proxy pattern is usually used to control access of resources. For instance, if you only need to use some methods at a particular moment, there is no point in instantiating an initializing a costly object until the resources are needed | Considering the following example which is base on a calculator of prime numbers, we can see that there is no need to do the heavy computation until getNumberOfPrimes is called, therefore the proxy pattern could be a solution to lazily perform the computation when the data is actually needed | This pattern hides the original class behaviour to the client. Therefore, the risk of using this pattern it is that you might not be aware of what is happening behind the scenes, and code could be inefficient | |
 **Adapter** | This pattern wraps an interface of a class into another interface that is required by the client. so it works as a bridge between to classes | It used when a specific interface is required by the client | This pattern can be apply when the target interface and the one required by the client are very similar | The adapter class should do only the necessary to adapt the original class, so no additional logic should be added to the adapter | In case that the interface required by the client and the Adaptee are very different, it will require further modifications in order to make them compatible |

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
