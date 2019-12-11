---
title: Get Started With Mockito And Spring MVC
tags: [Testing, Java, Mockito, Spring]
style: fill
color: warning
description: Mockito is a popular mocking framework for Java application. This framework helps you to test classes in isolation and avoids creating collaborators in your unit tests.
---

{%- capture list_items -%}

{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

## Introduction

[Mockito](https://site.mockito.org) is a popular mocking framework for **Java** application. This framework helps you to test classes in isolation and avoids creating collaborators in your unit tests. This need is very common in a web applications that uses the MVC pattern. For instance you might need to test your service layer which uses an utility class to check the payment method and then will call an API to process the payment. In this situation you have to instanciate or inject your utility class and the API client, and the second one will required to start a mock server. As you can see this looks like an integration test instead of a simple unit test, and we are not testing the service layer in isolation becuase it requires that both the utility class and the API client are free of bugs.

You can start using mockito by adding the following Maven dependency:

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>2.28.2</version>
    <scope>test</scope>
</dependency>
```

or with Gradle:

```groovy
repositories { jcenter() }
dependencies { testCompile "org.mockito:mockito-core:2.+" }
```


