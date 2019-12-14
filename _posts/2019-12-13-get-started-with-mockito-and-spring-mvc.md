---
title: Get Started With Mockito And Spring MVC
tags: [Testing, Java, Mockito, Spring]
style: fill
color: warning
description: Mockito is a popular mocking framework for Java application. This framework helps you to test classes in isolation and avoids creating collaborators in your unit tests.
---

{%- capture list_items -%}
Introduction
What is a mock?
Mockito Setup
Create a Mock
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

## Introduction

[Mockito](https://site.mockito.org) is a popular mocking framework for **Java** application. This framework helps you to test classes in isolation and avoids creating collaborators in your unit tests. This need is very common in a web applications that uses the MVC pattern. For instance you might need to test your service layer which uses an utility class to check the payment method and then will call an API to process the payment. In this situation you have to instanciate or inject your utility class and the API client, and the second one will required to start a mock server. As you can see this looks like an integration test instead of a simple unit test, and we are not testing the service layer in isolation becuase it requires that both the utility class and the API client are free of bugs.

## Behavior Driven Development

A test should be split into three blocks, each with a specified responsibility.

- **GIVEN**: Mock initialization, configuration and stubs.
- **WHEN**: Call to the _SUT_ (System Under Test) which can a method call.
- **THEN**: Assertions and verifications.

## What is a mock?

A **mock** is a proxy to the original object, so if a method of a proxy object is called then the proxy object will decide what to do. When you do something like `Mockito.mock(Foo.class)` you are simply creating a proxy of object of the _Foo_ class.

Mockito uses [CGLIB](https://github.com/cglib/cglib) which is based on [ASM](https://asm.ow2.io). **CGLIB** generates proxy classes by extending the original class, and all the overriden methods will call [MethodInterceptor.intercept()](http://cglib.sourceforge.net/apidocs/net/sf/cglib/proxy/MethodInterceptor.html#intercept%28java.lang.Object,%20java.lang.reflect.Method,%20java.lang.Object[],%20net.sf.cglib.proxy.MethodProxy%29) if the proxy callback is not null otherwise the super method (original method) will be called. This means that proxy classes have some limitations: 

- Final classes cannot be mocked.
- Final, private or static methods cannot be intercepted.

>To create proxy classes of final classes you need to perform bytecode transformations. e.g. remove the `final` modifier.

>**AMS** is a framework to manipulate Java bytecode.

## Mockito Setup

You can start using mockito by adding the following Maven dependency:

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>2.28.2</version>
    <scope>test</scope>
</dependency>
```

If you use Gradle:

```groovy
repositories { jcenter() }
dependencies { testCompile "org.mockito:mockito-core:2.+" }
```

>Mockito is also included in `spring-boot-starter-test`.

## Create a Mock

You can use an static method or an annotation to create a mock.

```java
FooRepository fooRepository = Mockito.mock(FooRepository.class);
```

```java
@Mock
private FooRepository fooRepository;
```

## Stub Methods

Any mocking framework allows you to return the desired value when a particular method is called, and with Mockito this can be achieved with `Mockito.when()` in conbination with `thenReturn()`. This is what we call a stub.

```java
when(fooRepository.getFooMessage()).thenReturn("Hello");
```

How does is work? A proxy is dinamically defined, so when the static method `Mockito.when()` is called, the passed method is stored. Then, when `thenReturn()` is called the value is also stored to the saved method. Finally, if the same proxy method with the same parametes is called again it will return the stored value this time.

You can stub the same method multiple times:

```java
when(fooRepository.getNumber(0)).thenReturn(8);
when(fooRepository.getNumber(1)).thenThrow(new ArrayIndexOutOfBoundsException());
```

Mockito also has other static methods that can be combined with the `when()` method like the one used in the previous example `.thenThrow()`.


Method | Description
---------|----------
 `thenReturn(T value)` | Returns given value 
 `thenThrow(Throwable throwableType)` | Throws given exception 
 `thenAnswer(Answer<?> answer)` | It's use to return custom answers 
 `then(Answer<?> answer)` | Alias of `thenAnswer()`
 `thenCallRealMethod()` | The original method is called. It's a partcial mock.

 >`thenCallRealMethod()` cannot be used with mocked interfaces or abstract classes. No data from the original class is initialized.
