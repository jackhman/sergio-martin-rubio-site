---
title: Micronaut, a Contender for the Microservices Era
tags: [Java, Web Applications]
style: fill
color: primary
description: Find out more about Micronaut, the next generation web framework which competes with Spring Boot
---

{%- capture list_items -%}
Introduction
Getting Started
When should I use Micronaut?
Caveats
{%- endcapture -%}

{% include list.html title="Content" type="toc" %}

## Introduction

**[Micronaut](https://micronaut.io/)** is a web framework similar to **Spring Boot**, which started at [OCI](https://objectcomputing.com/) and was initially developed by _Groovy_ and _Rails_ developers from **Pivotal**.

Micranaut is really a competitor for Spring Boot, and if you are comfortable with Spring Boot, Micronaut provides a Spring-like way of creating web applications. It uses JAX-RS annotations and specific Micronaut annotations similar to the Spring Boot ones (sometimes the only difference is the package name (`io.micronaut`)).

## Getting Started

A **CLI tool** similar to the one provided by Spring, is avaialable to create your Micronaut applications. The simplest way to start using the CLI tool is to install it through SDKMAM.

```shell
curl -s https://get.sdkman.io | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
sdk install micronaut
mn --version

```

Now you can create your first Micronaut project.

```shell
mn create-app com.sergiomartinrubio.micronaut-example --build maven
```

By default, it uses **Gradle** build system and Java as the main language, however, you can tweat these setting by using these two flags: `--build` [gradle, manven] and `--lang` [java, groovy, kotlin]. You can also add features to the project, like **GraalVM** support, [**Zipkin**](http://sergiomartinrubio.com/articles/troubleshooting-tools-for-microservices-architecture) for tracing, type of rendering...

>**GraalVM**: it is an ecosystem which supports many languages and allows you to run your applications like a native one, so in the case of Java, it will translate your bytecode into machine code.

When `nm` command is run, it esentially creates a base folder with the build system file chosen (if you choose Gradle, it also provides a Gradle wrapper, so it downloads it for you), a **Dockerfile** ready to run, and a logback file for log configuration.

## Features

You can crate controllers by using `@Controller` annotation and define your endpoints with http method annotations like `@Get`. When you run the application, you can hit the endpoint at http://localhost:8080, so it will feel as a **Spring Boot** application.

```java
@Controller
public class UpperCaseController {

    @Get("/touppercase/{input}")
    public String toUpperCase(@PathVariable("input") String input) {
        return input.toUpperCase();
    }
}
```

Test are also created in a similar way as the **Spring Boot** way. Instead of 
`@SpringBootTest` annotation, it uses `@MicronautTest`, and `RxHttpClient` annotated with `@Client` instead of autowiring `TestRestTemplate`, to make HTTP requests to your controller.

```java
@MicronautTest
class UpperCaseControllerTest {

    @Inject
    @Client("/")
    RxHttpClient client;

    @Test
    public void toUpperCase_givenStringPathParam_whenParamIsValid_thenReturnsStringInUpperCase() {
        HttpRequest request = HttpRequest.GET("/touppercase/test");
        String response = client.toBlocking().retrieve(request);

        assertNotNull(response);
        assertEquals("TEST", response);
    }
}
```

<p class="text-center">
{% include button.html link="https://github.com/smartinrub/micronaut-example" text="Source Code" %}
</p>

Micronaut provides a compile-time _AOP API_ that does not use reflection. For those who don't know what reflection is, it is basically a way to the program to introspect itself, so it asks itself if a class contains specific methods, annotations, parameters... and Spring uses intensively reflection in order to search for controllers, endpoints, beans... so there is a performance penalty that you pay, and most of this work is done during startup. Therefore, Micronaut has got a great advantage in the serverless model, where startup times are very important.

Micronaut relies on annoations, it uses these annotations in a differnt way as Spring. During compile-time, it scans all the different annotations and add extra code to hook everything up. This means that once the Java jar is compiled annotations are not needed anymore, so there is no need to do any reflection, because the code is already baked into the jar. Bear in mind that Micranaut doesn't modify the original classes, instead it creates additional classes, so in case you need to debug your code, it will stop on the line you are expecting to stop.

{% include figure.html image="https://lh3.googleusercontent.com/nGgRatrpzZdn-_ZvPwut4KXsFHZqmpOy-EGr7QuDAPuafOhqCQKE4Xon8XxvXV25g9F_BthT9XC6CNP3raJoSk6O10WoHLivEhiWnFRQax5ImF81t4RABMBsjxm6p45wBq8Ml_M4jQ=w400" caption="Micronaut Additional Classes" %}

Micranaut tries to make it compatible with **GraalVM** as much as possible, and even has a feature to generate a project with Graal support. This is done by creating a Docker image with GraalVM, _JDK_ and the Java binaries.

**Micronaut only supports Netty** and **there is no way at the moment to switch to Tomcat**, so you have to considerer if Spring metrics and threading handling of Tomcat is a must. However, it is compatible with **Micrometer**, **provides refresh and healthcheck endpoints, cache endpoints...**.

Other features:
- **Bean configuration and lifecycle modifications**.
- A number of different formats for your **application properties** like application.yaml, application.properties or application.groovy.
- _AOP_.
- **Environment specific configuration**.
- **AWS Lambda**.
- [OpenFaaS](https://github.com/openfaas/faas), [ProjectFn](http://sergiomartinrubio.com/articles/serverless-hello-world-with-fn-framework) and [riff](https://github.com/projectriff/riff).
- **Cloud Native support**: _Consul_, _Eureka_, _Kubernetes_, _Spring Cloud Config_...
- **Tracing** with **Zipkin**.
- **Ribbon**.
- Kafka and RabbitMQ.
- **Support for data access configuration**: _mongoDB_, _Hibernate_, _Redis_, _Cassandra_...
- **Authentication**: LDAP...
- **Circuit Breaker** (Hystrix).
- **Websockets**.
...

## When should I use Micronaut?

**Micronaut** team advertise it as a low memory compsumption framework with a quick startup time, so it is very suitable for the _serverless_ world.

This framework is also a good choice if you are moving your application to the cloud or you are creating a project from scratch.

## Caveats

- It is still fairly new, and the community is quite small compare to the Spring one, though big enterprise companies like ThoughtWorks has it already on their tech radar.
- Netty is the only client web server available.
- For new developers who are used to Spring, it might be a little big challenging, because, under the hood it works in a completaly different way.
