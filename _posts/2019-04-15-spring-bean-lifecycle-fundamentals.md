---
title: Spring Bean Lifecycle Fundamentals
tags: [Java, Spring]
style: fill
color: success
description: Bean is one of the angular stones in the Spring Framework. What are you waiting for to learn more about it?
---

{%- capture list_items -%}
Introduction
{%- endcapture -%}

{% include list.html title="Content" type="toc" %}

## Introduction

**Bean** is one of the angular stones in the **Spring Framework**, and it is very important to know how it works. However, the concept of _bean_ it is not very clear and the [Bean definition given by the official Spring documentation](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-introduction) is not easy to understand.

>In Spring, the **objects** that form the backbone of your application and that are **managed by the Spring IoC container** are called beans. **A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container**. Otherwise, **a bean is simply one of many objects in your application**. Beans, and the dependencies among them, are reflected in the configuration metadata used by a container.

{% include highlight.html text="Roughly, we can say that a **Bean** is simply an object with a concrete life cycle managed by the **Spring Framework**." %}

This definition might seem a little bit abstract, but do not worry we will go into more in depth shortly.

Moreover, I would like to answer some **frequest Java interview questions** like:

- Can you explain Bean life cycle in Spring Bean Factory Container?
- What is auto-wiring?
- What are different types of auto wiring in Spring?
- What are different Bean scopes in Spring?

## Main Concepts

### Spring IoC

According to the Spring definition of **Bean**, this special object is managed by the Spring IoC container, which is represented in your application by `org.springframework.context.ApplicationContext`, and this container is responsible for the initiliazation, configuration and assembly. But we will not have to worry about enriching (add metadata) your bean, since it is done by the framework by using XML config files, annotations or Java code.

A Spring IoC container can manage one or more beans and once all the beans are loaded in this container, you can retrieve instances of your beans through the `ApplicationContext` (`getBean()`).

### Dependency Injection

The definition of DI given by Spring is very straightforward, and simply says that DI is the process of defining objects with their dependencies, that are set on the object instance through a constructor or setters.

**DI** is used by the **IoC container**, which injects the dependencies when the bean is created. Therefore, it is not the bean, it is the container the one reposible for injecting the dependencie. The Bean does not need to know anything about the instantiation and location of the dependencies, and that is why it is called Inversion of Control.

### Bean Scopes



