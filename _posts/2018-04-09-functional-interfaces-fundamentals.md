---
title: Functional Interfaces Fundamentals
tags: [Java]
style: fill
color: primary
description: Learn more about Java functional interfaces!
---

{%- capture list_items -%}
Introduction
Annotation
Use cases for Functional Interfaces
Default methods
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

# Introduction

**Functional interfaces** are the ones which only have one abstract method different from the public methods of `Object.class` (_equals_, _hashCode_…), so that the contract is only one function.

# Annotation

```java
@FunctionalInterface
interface Foo {
	void method();
}
```

`@FunctionalInterface` annotation is not mandatory but highly recommended, because the interface will be checked at compiling time if in fact, your interface is functional. Moreover, this annotation makes your architecture easier to understand.


# Use cases for Functional Interfaces

Since **Java 8** has been released functional interfaces are very useful when combining with lambda expressions.

```java
@FunctionalInterface
interface Foo {
	String method(String string);
}
```

And you can use it like this:

```java
Foo foo = string -> string + "world!";
System.out.println(foo.method("Hello "));
```

It is important to mention that **Java 8** already provides some functional interfaces out-of-the-box in `Function<T,R>` from the `java.util.function`. Therefore, in same cases as the explained before we can make use of it.

```java
Function<String, String> function = string -> string + "world!";
System.out.println(function.apply("Hello "));
```

# Default methods

**Default methods** are allowed in functional interfaces since they are not abstract methods.

```java
@FunctionalInterface
interface Foo {
	String method(String string);
		
	default String defaultMethod() {
		return "Hello World!";
	}
}
```

Although, you are allowed to define default methods in functional interfaces, it is not a good practice overuse this technique, because default methods might clash with each other when your interface extends several interfaces with default methods.