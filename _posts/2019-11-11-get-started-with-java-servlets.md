---
title: Get Started With Java Servlets
tags: [Java, Web Applications]
style: fill
color: danger
description: Java Servlets are the core technology of many web application frameworks like Spring, that's why it's very important to know the fundamentals of Servlets. Click here to keep reading!
---

{%- capture list_items -%}
What Is a Servlet?
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

# What Is a Servlet?

A **Java Servlet** is simply a class that extends from one of the classes in `javax.servlet.http` package and is used in applications to handle network communication like _HTTP request-response_ model.

_Servlets_ live in **Servlet Containers**. A container is a server program which can receive network requests and redirect those requests to a Servlet object. It also handles many tedious tasks like opening sockets; tranformations to convert HTTP into Java API calls; or provides a set of interfaces to write your own servlet implementation.

>Note: Servlets and JSPs require Servlet Containers liket Apache [Tomcat](http://tomcat.apache.org/), [WildFly](https://wildfly.org/), etc.. 

# How a Server Request is processed by the Web Server

1. The web server receives the HTTP request.
2. The request is forwarded to the _Servlet Container_.
3. The Servlet Container creates two objects the `HttpServletRequest` and `HttpServletResponse`.
4. The container creates a thread for the request, passes the request and reponse objects as arguments and delegates the request to a particular Servlet chosen between the Servlets it contains.
5. The `service()` method is executed and evaluates which Servlet method will be called (`doGet()`, `doPost()`, `doPut()`, `doDelete()`, `doHead()`, ...) based on the request sent by the client (`GET`, `POST`, `PUT`, `DELETE`, `HEAD`, ...).
6. The Servlet handles the client request and generates content to be returned to the client.
7. The container sends the Servlet response to the client.

>Note: `ServletRequest` contains parameters such as name, values, attributes, and an input stream.
