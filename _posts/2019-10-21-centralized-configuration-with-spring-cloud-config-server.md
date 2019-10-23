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
Encrypted Properties
Symmetric
Asymmetric
Other Features
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

## Encrypted Properties

Spring Cloud Config Server provides support to ecrypt property values. You can use either symmetric or asymmetric key, however Spring recommends to use a symmetric key since you will only need to configure a single value in the bootstrap property file.

The server also exposes `/encrypt` and `/decrypt` endpoints.

To encrypt a value:

```shell
curl localhost:8888/encrypt -d mysecret
```

and to decrypt it:

```shell
curl localhost:8888/decrypt -d 682bc583f4641835fa2db009355293665d2647dade3375c0ee201de2a49f7bda
```

>Note: The encrypted vales requires the prefix `{cipher}` when you put it in the YAML or properties.

### Symmetric

```yml
encrypt:
  key: ${ENCRYPT_KEY} # any string
```

To configure a symmetric key, you need to set `encrypt.key` to a plain text string in the Spring _Cloud Config Server_ (the client does not require this key since values are decrypted automatically before they are sent to the client, see below) and use the `ENCRYPT_KEY` environment variable to keep it out of plain-text repository files, for example you can pass it using Jenkins while generating a pipeline.

>Important: [Automatic decryption is not working for 2.2.0.M3](https://github.com/spring-cloud/spring-cloud-config/issues/1493) when headers contain `application/vnd.spring-cloud.config-server.v2+json`, since `includeOrigin` is set to true. This is happening because the property source now contains a  `PropertyValueDescriptor` instead of a string value, so `toString()` method returns the object address. In the meantime you are force to decrypt the values in the client by setting the `encrypt.key` property.

>Note: `ENCRYPT_KEY` overrides `encrypt.key` value.

You can also disable decryption of properties before sending them to the client. To do this you have to set `spring.cloud.config.server.encrypt.enabled` to `false` (`true` is the default value). When this property is set to `false`, `encrypt.key` is required in the client for decryption during start up.

### Asymmetric

The symmetric key is superior in terms of security, but it is usually less convinient since you have to set a few properties.

To configure an asymmetric key use a keystore. This will require a password which itself would have to be stored unencrypted and set the keystore password as an environment variable.

```shell
keytool -genkeypair -alias myKeyStore -keyalg RSA \
  -dname "CN=Web Server,OU=Unit,O=Organization,L=City,S=State,C=US" \
  -keystore server.jks -storepass password123
```

then export the environment variable as we did for symmetric encryption:

```shell
export ENCRYPT_KEY=password123
```

and finally set the keystore configuration in both server and client:

```yml
encrypt:
  key-store:
    location: classpath:keys/server.jks
    password: ${ENCRYPT_KEY}
    alias: myKeyStore
```

>Note: If a value cannot be decrypted the value will be replaced with `<n/a>`.

## Dynamic Configuration Changes

Another important feature of Spring Cloud Config Server is the posibility of refreshing the client configuration at runtime, without restarting the JVM, and you only need to enable the `/refresh` endpoint to take adavantage of this feature.

Go to your client application properties file and include the endpoint:

```yml
management:
  endpoints:
    web:
      exposure:
        include: refresh
```

>Note: `spring-boot-starter-actuator` dependency is required in order to active management endpoints.

## Other Features

- Set multiple repositories with `spring.cloud.config.server.git.repo`.
- Set the timeout for retrieven properties from git  with `spring.cloud.config.server.git.timeout` (default value is 5 seconds).
- Force the pull from git if you set `spring.cloud.config.server.git.force-pull` to true.

<p class="text-center">
{% include elements/button.html link="https://github.com/smartinrub/spring-centralized-configuration" text="Source Code" %}
</p>
