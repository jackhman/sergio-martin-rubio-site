---
title: Moving Beyond REST with GraphQL
tags: [Java]
style: fill
color: success
description: GraphQL is an innovative query language which allows you to get what you ask for in a single request. What are you waiting for to know more about GraphQL?
---

{%- capture list_items -%}
Introduction

{%- endcapture -%}

{% include list.html title="Content" type="toc" %}

## Introduction

[**GraphQL**](https://graphql.org/) is a **query language** that matches up with **Domain Driven Design**, so you can use your existing entities or models in your GraphQL design.

This query language was created by **Facebook** and **open sourced** later **in 2015**, and since then it has been **maitained by the community**.

To start using **GraphQL** you will have to learn a **new specification**, because it is not a simple implementation, however it is pretty **simple** and if you are familiar with other query languages it will take you a couple of days to understand how it works. The [GraphQL spec](https://graphql.github.io/graphql-spec/June2018/#) is very well documented and shows you how to use operations like queries or mutations, define schemas or what good practicies you should follow.

Companies like _Pivotal_ are already supporting _GraphQL_, in fact it was one of the topics presented in [Spring IO 2019](https://2019.springio.net/sessions/moving-beyond-rest-graphql-and-java-spring).

## Specification

This API Query Language allows you to retrive data from a service in one go. How can I do that? A single endpoint is exposed by GraphQL, and given a schema which contains your models and operations, you can make HTTP requests to this single endpoint by providing operation names and fields. 

GraphQL supports both, GET and POST HTTP methods. In case of GET, we have to use a query parameter (`?query={operationName{field}}`) and do URL encoding. On the other hand, we could do a standard POST request with a JSON payload.

e.g.

```json
{
  "query": "...",
  "operationName": "...",
}
```

<p class="text-center">
{% include button.html link="https://github.com/smartinrub/spring-boot-graphql" text="Source Code" %}
</p>
