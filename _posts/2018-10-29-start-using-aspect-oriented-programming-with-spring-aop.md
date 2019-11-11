---
title: Start using Aspect Oriented Programming with Spring AOP
tags: [Java, AOP, Spring, Aspects]
style: fill
color: primary
description: Aspect Oriented Programming (AOP) helps us to solve specific situations in a very elegant way and is used to insert code depending on how our code looks.
---

{%- capture list_items -%}
Introduction
Spring AOP
Concepts
How to create Aspects
Advice Types
Advantages over AspectJ
Disadvantages over AspectJ
Conclusion
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

## Introduction

**Aspect Oriented Programming (AOP)** helps us to solve specific situations in a very elegant way and is used to insert code depending on how our code looks.

Why aspects? When we have a concern which is cross domain aspects are a good solution. e.g. logs, exceptions, security…

[AspectJ](https://www.eclipse.org/aspectj/) is the AOP Java framework commonly used when we want to use aspects on our application.

How does AspectJ work? The AOP framework does not look at you actual code, it looks at the byte code, so we give patterns to search for, and AspectJ inserts the code where the match is found.

There are other AOP implementations such as [Spring AOP](https://docs.spring.io/spring/docs/4.3.15.RELEASE/spring-framework-reference/html/aop.html) or [JBoss AOP](http://jbossaop.jboss.org/) that are built on top of AspectJ and simplify its use.

## Spring AOP

### Concepts

- **Aspect**: in **Spring AOP**, aspects are declared using classes annotated with `@Aspect`.
- **Join Points**: are the possible points in the program execution where our code can be inserted. e.g. method call, exception thrown…
- **Advice**: is the action taken by an aspect on a particular join point.
- **Pointcut**: is where you cut the flow of the program to add our advice (some code). We use patterns to specify where to put the code. e.g. the name of a method.
- **Weaving**: process of finding a match and inserting the code that you want to execute.

### How to create Aspects

To create an aspect you need to have a class annotated with `@Aspect` and register the aspect as a regular bean in your **Spring XML** configuration or add the `@Component` annotation. Once the class is created with the annotations required, you can declare as many *Advice* as you want inside.

```java
@Aspect
@Component
public class MyBeforeAspect {
    
    @Before("org.smartinrub.aopspringdemo.aspect.JoinPoints.serviceLayer()")
    public void beforeSomething(JoinPoint joinPoint) {
        log.info("ASPECT - Before: {}", joinPoint.getSignature());
    }
}
```

As a good practice, we will also create another class where all join points will be declared, so we can refer the join points from an advice annotation.

```java
public class JoinPoints { 
    
    @Pointcut("execution(* org.smartinrub.aopspringdemo.service.*.*(..))")
    public void serviceLayer(){}

    @Pointcut(
            value = "execution(* org.smartinrub.aopspringdemo.repository.*.*(..)) && args(id)",
            argNames = "id")
    public void repositoryLayer(int id){}

    @Pointcut("execution(* org.smartinrub.aopspringdemo.controller.*.*(..))")
    public void controllerLayer(){}
}
```

### Advice Types

- `@Before`: it runs before a join point.
- `@After`: it runs after a method call
- `@AfterReturning`: it runs when a join point completes successfully and after @After point join.
- `@AfterThrowing`: it runs when a method exits because of an exception.
- `@Around`: it runs before and after the method execution. It allows us to proceed to the join point, return its own object or throw an exception.

#### @Before

```java
@Before("org.smartinrub.aopspringdemo.aspect.JoinPoints.serviceLayer()")
public void beforeSomething(JoinPoint joinPoint) {
    log.info("ASPECT - Before: {}", joinPoint.getSignature());
}
```

In this example, apart from the `@Before` annotation which will make run the advice body before the method execution, we can notice that `JoinPoint` variable was added to the method signature. `JoinPoint` allows us to get some information from the target method.

#### @After

```java
@After(value = "org.smartinrub.aopspringdemo.aspect.JoinPoints.repositoryLayer(id)")
public void afterSomething(JoinPoint joinPoint, int id) {
    log.info("ASPECT - After: {}", joinPoint.getSignature());
}
```

In this example `@After` will make run the advice after the method execution. A parameter name was also added to this example to show how we can make arguments available to the advice body. Therefore, when the method that satisfies the pointcut pattern runs, the advice will be able to use the value.

#### @AfterReturning

```java
@AfterReturning(
        value = "org.smartinrub.aopspringdemo.aspect.JoinPoints.repositoryLayer(id)",
        returning = "result")
public void afterReturning(JoinPoint joinPoint, Object result, int id) {
    log.info("ASPECT - After Returning: repository {} with id {} returned {}", joinPoint, id, result);
}
```

`@AfterReturning` allows us to run some code after the method returns a value. If you need access to the returned value, we can use the “returning” attribute on the `@AfterReturning` annotation and add the same value name on the advice signature to make it available in our advice body.

#### @AfterThrowing

```java
@AfterThrowing(
        value = "org.smartinrub.aopspringdemo.aspect.JoinPoints.controllerLayer()",
        throwing = "ex")
public void afterThrowing(JoinPoint joinPoint, IllegalArgumentException ex) {
    log.info("ASPECT - After Throwing: exception \"{}\" on controller \"{}\"", ex, joinPoint.getSignature());
}
```

This example shows how to run some code when an exception is thrown, and expose the exception in your advice body. We have to do the same as we did for `@AfterReturing`, but in this case the annotation attribute is “throwing“.

#### @Around

```java
@Around("@annotation(org.smartinrub.aopspringdemo.annotation.TrackTime)")
public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
    long startTime = System.currentTimeMillis();
    log.info("ASPECT - Around: Time before proceed: {}", startTime);
    
    Object proceed = joinPoint.proceed();
    
    long totalTime = System.currentTimeMillis() - startTime;
    log.info("ASPECT - Around: Time after proceed {}: {}", joinPoint.getSignature(), totalTime);
    return proceed;
}
```

This is the most complex advice. `@Around` includes before, after, throwing and returning functionalities.

The first parameter of this advice must be `ProceedingJoinPoint`, and needs to be called with `proceed()` to execute the method. The value returned by the advice will be the same as the value of the target method.

In this examples we also make use of an annotation as a join point pattern, so this advice will be only executed when the annotation TrackTime is found.

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
    public @interface TrackTime {   
}
```

#### @TrackTime

```java
@Override
public String getSomething(int id) throws InterruptedException {
    Thread.sleep(2000);
    return repositoryOne.getSomething(id);
}
```

### Advantages over AspectJ

- It is simpler to use than **AspectJ**, since we do not need the [AspectJ compiler](https://www.eclipse.org/aspectj/doc/next/devguide/ajc-ref.html) or **LTW** ([load-time weaving](https://www.eclipse.org/aspectj/doc/released/devguide/ltw.html)).
- It uses **proxy pattern** and **decorator pattern**.

### Disadvantages over AspectJ

- **Spring AOP** is proxy based, so you can only use method-execution join points.
- There is some runtime overhead.

## Conclusion

**Aspect-oriented programming** is intended to address common problems that **object-oriented programming** doesn’t address well and can avoid code duplication in some situations. However, AOP is a extreme solution that can hide parts of your code and could make debugging a difficult task, so it can cause more harm than good.

<p class="text-center">
{% include elements/button.html link="https://github.com/smartinrub/aop-spring-demo" text="Source Code" %}
</p>