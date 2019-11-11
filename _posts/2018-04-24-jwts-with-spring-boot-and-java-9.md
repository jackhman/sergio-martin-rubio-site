---
title: JWTs With Spring Boot and Java 9
tags: [Java, Spring, Security]
style: boder
color: success
description: Interested in using JWTs for your Java 9/Spring Boot projects? Learn how to set up your dependencies and the use cases for you to consider.
---

{%- capture list_items -%}
Introduction
Features
How Does It Work?
JSON Web Token format
Java Integration
Troubleshooting
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

# Introduction

[JWT](https://jwt.io/) (_JSON Web Token_) is an open source standard commonly used to transmit data between two services in a compact and secure way. This standard offers a wide range of libraries to generate _JWTs_ and includes libraries for platforms such as _.NET_, _Python_, _Node.js_, _Java_, _JavaScript_, _Perl_, _Ruby_, _Elixir_, _Golang_, _Groovy_, and _Haskell_.

# Features

* **Secure**. _JWTs_ are signed by using a secret or a **public/private key pair**.
* **Self-contained**: all information is stored inside the _JWT token_. No need for further database calls.
* **Unmodifiable information, but exposed**: Do not put secrets inside _JWTs_.
* **Compact format**: _JWTs_ are really small.

# How Does It Work?

{% include elements/figure.html image="https://lh3.googleusercontent.com/6C42ufo6FCCFM40a24XGl4SkJ7a4IDlM-8C5iGGTwfB2W7oj6JBSZPx9QeF4QZnba4TXQTdNZsldVeIkWQ=w800" caption="JWT Communication" %}

1. _JWTs_ are sent through a URL, _POST_ parameter or inside the header.
2. Contains a payload with information about the sender, issued date, expiration date...
3. _JWTs_ are usually generated during the authentication phase, and once the user is logged in, the _JWT_ previously generated allows the user to access to resources without having to make additional calls to databases.
4. They can be used to exchange information between services in a secure way, making sure that was not modified during the transmission, because the signature is calculated using the header and payload.
5. The token is storage locally or in a cookie.
6. When the user wants to use the token the convention is to add it to the header in the _Authorization_ field using the Bearer prefix followed by the token value.

```shell
Authorization: Bearer
```

## JSON Web Token format

**JWTs** are structured in three parts separated by dots:

- **Header**: contains the **token type** (_jwt_) and **hashing algorithm** like _SHA256_ or _RSA_.
- **Payload**: contains claims, which are the metadata like **subjects**, **senders**, or **expiration date**. There are three kinds of claims: **registered**, **public**, and **private**.
- **Signature**: the combination of **header**, **payload**, **secret**, and the **algorithm** specified in the header. The signature ensures that nothing has changed.

Therefore, the format is **xxx.yyy.zzz**

## Java Integration

**Java JWT** provides all you need to generate signed tokens in a _Java_ application.

**Add maven dependency**:

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.0</version>
</dependency>
```

**Generate JWT Token**:

```java
String jwt = Jwts.builder()
    .setClaims(claims)
    .setSubject(email)
    .setIssuedAt(new Date())
    .setExpiration(new Date(System.currentTimeMillis()+ JWT_EXPIRATION_TIME))
    .signWith(SignatureAlgorithm.HS256, JWT_SECRET)
    .compact();
```

- **Issuer** (iss): `getIssuer()` and `setIssuer(String)`
- **Subject** (sub): `getSubject()` and `setSubject(String)`
- **Audience** (aud): `getAudience()` and `setAudience(String)`
- **Expiration** (exp): `getExpiration()` and `setExpiration(Date)`
- **Not Before** (nbf): `getNotBefore()` and `setNotBefore(Date)`
- **Issued At** (iat): `getIssuedAt()` and `setIssuedAt(Date)`
- **JWT ID** (jti): `getId()` and `setId(String)`

All these parameters are available through **Claims** getters.

**Verify JWT Token**:

```java
final Claims claims = Jwts.parser()
    .setSigningKey(JWT_SECRET)
    .parseClaimsJws(compactJws)
    .getBody();
```

- `JWT_SECRET` is the secret used when we generated the token.
- `compactJws` is the _JWT token_ pass in the header.

If the verification fails, a `SignatureException` will be thrown. Otherwise, we will be able to store the metadata in a claims variable that will be available to consume later.

## Troubleshooting

In case you are using **Java 9**, make sure you add an explicit dependency to the _POM file_.

```xml
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
</dependency>
```

`jaxb-api` is missing in _Java 9_, and until **jjwt** adds the dependency or removes the usage of _JAXB classes_, we have to add it manually.

<p class="text-center">
{% include elements/button.html link="https://github.com/smartinrub/jwt-example" text="Source Code" %}
</p>
