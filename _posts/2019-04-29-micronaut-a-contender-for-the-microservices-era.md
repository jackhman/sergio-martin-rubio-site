---
title: Micronaut, a Contender for the Microservices Era
tags: [Java, Web Applications]
style: fill
color: primary
description: Find out more about Micronaut, the next generation web framework which competes with Spring Boot
---

{%- capture list_items -%}
Introduction
When should I use Micronaut?
{%- endcapture -%}

{% include list.html title="Content" type="toc" %}

## Introduction

**Micronaut** is a web framework similar to **Spring Boot**, wich started at [OCI](https://objectcomputing.com/) and was initially developed by _Groovy_ and _Rails_ developes from **Pivotal**.

This framework offers _serverless_ and **GraalVM** support out-of-the-box.

Micranaut is really a compatitor for Spring Boot, and if you are comfortable with Spring Boot, Micronaut provides a Spring-like way of creating web applications. It uses JAX-RS annotations and specific Micronaut annotations similar to the Spring Boot ones, and sometimes the only difference is the package (`io.micronaut`).

## When should I use Micronaut?

**Micronaut** team advertise it as a low memory compsumption framework with a quick startup time, so it is very suitable for the _serverless_ world.

## Getting Started

A CLI tool similar to the one provided by Spring, is avaialable to create your Micronaut application. The simples way to start using the CLI tool is to install it through SDKMAM.

```shell
curl -s https://get.sdkman.io | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
sdk install micronaut
mn --version

```

Now you can create a new project.

```shell
mn create-app com.sergiomartinrubio.micronaut-example --build maven
```

By default, it uses **Gradle** build system and Java as the main language, however, you can tweat these setting by using these two flags: `--build` [gradle, manven] and `--lang` [java, groovy, kotlin]. You can also add features to the project, like **GraalVM** support, [**Zipkin**](http://sergiomartinrubio.com/articles/troubleshooting-tools-for-microservices-architecture) for tracing, type of rendering...

When `nm` command is run, it esentially creates a base folder with the build system file chosen (if you choose Gradle, it also provides a Gradle wrapper, so it downloads it for you), a **Dockerfile** ready to run, and a logback file configuration.

## Features

You can crate controllers by using `@Controller` annotation with, for instance, `@Get` to define your endpoints, and when you run the application you can hit the endpoint at http://localhost:8080, so it will feel as a **Spring Boot** application.

**Micronaut** support a number of different format for your application properties like application.yaml, application.properties or application.groovy.

Micronaut provides a compile-time _AOP API_ that does not use reflection. For those who don't know what reflection is, it is basically a way to the program to introspect itself, so it asks itself if a class contains specific methods, annotations, parameters... and Spring uses intensively reflection in order to search for controllers, endpoints, beans... so there is a performance penalty that you pay, and most of this work is done during startup. Therefore, Micronaut has got a great advantage in the serverless model, where startup times are very important.

Micronaut relies on annoations, it uses these annotations in a differnt way as Spring. During compile-time, it scans all the different annotations and add extra code to hook everything up. This means that once the Java jar is compiled you don't need the annotations anymore, so there is no need to do any reflection, because the code is already baked into that jar. However, Micranaut doesn't modify the original classes, instead it creates additional classes, so in case you need to debug your code, it will stop on the line you are expecting to stop.

{% include figure.html image="https://lh3.googleusercontent.com/nGgRatrpzZdn-_ZvPwut4KXsFHZqmpOy-EGr7QuDAPuafOhqCQKE4Xon8XxvXV25g9F_BthT9XC6CNP3raJoSk6O10WoHLivEhiWnFRQax5ImF81t4RABMBsjxm6p45wBq8Ml_M4jQ=w400" caption="Micronaut Additional Classes" %}




Micronaut uses Netty



