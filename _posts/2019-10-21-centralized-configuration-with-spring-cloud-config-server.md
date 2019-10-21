---
title: Centralized Configuration With Spring Cloud Config Server
tags: [Spring, Spring Boot, Spring Cloud, Configuration, Microservices]
style: fill
color: success
description: Spring Cloud Config Server allows you to centralized your configuration in your microservices architecture across all evironments. Keep reading to learn how to set up the config server!
---

{%- capture list_items -%}
Introduction
Getting Started
Client Side
Server Side
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

# Introduction

[Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/) provides remote configuration for your Spring applications or any application running in any language. This tool has been out there for a long time, so it is very mature and reliable. Spring Cloud Config Server comes in handy when you need to set up configurations for microservices in multiple environments, so you can manage your properties separately and avoid the hassle of building your whole application when global variables are updated.

# Getting Started

By default _Spring Cloud Config Server_ uses git to store the configuration files, however you could write your own implementation and use a different VCS. 

Clients retrieve the configuration through the Spring Cloud Config Server and both client and server have to be mapped to the Spring `Environment`, which includes:

- `{application}` is `spring.application.name` on the client side.

- `{profile}` is `spring.profiles.active` on the client.

- `{label}` is the commit id, branch name, or tag.

Property files formats allowed:

```shell
 /{application}/{profile}[/{label}]
 /{application}-{profile}.yml
 /{label}/{application}-{profile}.yml
 /{application}-{profile}.properties
 /{label}/{application}-{profile}.properties
```

>Note: Active profiles take precedence over defaults. When multiple profiles are selected, the last one will be chosen.

>Note: If the git branch or tag name contains slashes (`/`) they must be replace with underscores (`_`).

## Client Side

The Maven dependency required on the client side is:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```

Then you only need to set a few Spring properties in `bootstrap.yml`:

```yml
spring:
  application:
    name: config-server-client
  cloud:
    config:
      uri: http://localhost:8888
      profile: development
```

where the `spring.application.name` will be mapped to the `{application}` part of the property file stored in git; `spring.cloud.config.uri` is the `Spring Config Server` address; and `spring.cloud.config.profile` will be mapped to the `{profile}` part of the property file. Addionally, you could set `spring.cloud.config.label` which would be mapped to the commit id, branch name, or tag. For the previous example you should have a property file named `config-server-client-development.yml` on your git config repo in order to use the remote cofiguration.

>Note: Remember that `bootstrap.yml` is loaded before `application.yml`, and it is needed if your application's configuration is stored on a remote configuration server and to decrypt properties in the local external configuration files.

## Server Side

The Maven dependency required on the server side is:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

By simply using the `@EnableConfigServer` annotation, the server is configured in the Spring Boot application.

```java
@EnableConfigServer
@SpringBootApplication
public class SpringConfigServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringConfigServerApplication.class, args);
    }
}
```

A couple of properties have to be set on the application property file:

```yml
server:
  port: 8888
spring:
  cloud:
    config:
      server:
        git:
          uri: https://github.com/smartinrub/spring-centralized-configuration.git 
```

where `server.port` is used to run the application in a different port from the client; and `spring.cloud.config.server.git.uri` is the url to the git repository where the property files are.

>Note: You could create a git repository in your local machine, commit the changes and set `spring.cloud.config.server.git.uri` to somthing like `file://${user.home}/config-repo` to test your configuration locally.

<p class="text-center">
{% include elements/button.html link="https://github.com/smartinrub/spring-centralized-configuration" text="Source Code" %}
</p>
