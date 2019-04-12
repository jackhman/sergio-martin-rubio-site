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






