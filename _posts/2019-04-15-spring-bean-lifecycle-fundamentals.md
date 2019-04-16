---
title: Spring Bean Lifecycle Fundamentals
tags: [Java, Spring]
style: fill
color: success
description: Bean is one of the angular stones in the Spring Framework. What are you waiting for to learn more about it?
---

{%- capture list_items -%}
Introduction
{%- endcapture -%}

{% include list.html title="Content" type="toc" %}

## Introduction

**Bean** is one of the angular stones in the **Spring Framework**, and it is very important to know how it works. However, the concept of _bean_ it is not very clear and the [Bean definition given by the official Spring documentation](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-introduction) is not easy to understand.

>In Spring, the **objects** that form the backbone of your application and that are **managed by the Spring IoC container** are called beans. **A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container**. Otherwise, **a bean is simply one of many objects in your application**. Beans, and the dependencies among them, are reflected in the configuration metadata used by a container.

{% include highlight.html text="Roughly, we can say that a Bean is simply an object with a concrete life cycle managed by the Spring Framework." %}

This definition might seem a little bit abstract, but do not worry we will go into more in depth shortly.

Moreover, I would like to answer some **frequest Java interview questions** like:

- Can you explain Bean life cycle in Spring Bean Factory Container?
- What is auto-wiring?
- What are different types of auto wiring in Spring?
- What are different Bean scopes in Spring?

## Main Concepts

### Spring IoC

According to the Spring definition of **Bean**, this special object is managed by the Spring IoC container, which is represented in your application by `org.springframework.context.ApplicationContext`, and this container is responsible for the initiliazation, configuration and assembly. But we will not have to worry about enriching (add metadata) your bean, since it is done by the framework by using XML config files, annotations or Java code.

A Spring IoC container can manage one or more beans and once all the beans are loaded in this container, you can retrieve instances of your beans through the `ApplicationContext` (`getBean()`).

### Dependency Injection

The definition of DI given by Spring is very straightforward, and simply says that DI is the process of defining objects with their dependencies, that are set on the object instance through a constructor or setters.

**DI** is used by the **IoC container**, which injects the dependencies when the bean is created. Therefore, it is not the bean, it is the container the one reposible for injecting the dependencie. The Bean does not need to know anything about the instantiation and location of the dependencies, and that is why it is called Inversion of Control.

### Bean Scopes

All the beans are not the same, and _Spring_ provides a few different recipes to modify the scope. In total there are six scopes:
- **_singleton_** (default): creates one instance for each **IoC container** (`ApplicationContext`). 
- **_prototype_**: a new instance is created every time the bean is called.
- **_request_**: creates a single instance per HTTP request.
- **_session_**: one instance for each web session.
- **_application_**: only one instance per `ServletContext`.
- _**websocket**_: it is tight to the websocket lifecycle.

>Do not confuse _Bean_ with scope singleton with the [Singleton Design Pattern](http://sergiomartinrubio.com/articles/creational-patterns-features#singleton-pattern). The latter creates an instance per `ClassLoader`, wherease the singleton Bean creates a bean per Spring container.

>When using singleton Bean which contains beans with different lifecycles, remember that injection only happens once. We can use beans with different scopes by using `proxyMode = ScopedProxyMode.TARGET_CLASS` in the `@Scope` annotation.

## Lifecycle

### Spring Bean Creation Lifecycle

{% include figure.html image="https://lh3.googleusercontent.com/nh9SbACTD7OEgewuv7DNNGRDlut2R6KZYVDgK5oouzwYAkchtR9VolnRvVmfbBhqV0SXwC_F0ywT-2cZJanHHs9YtIGZePd2k-vQ05wq_Qw0Jg4t6ony2tOLlv19grC5Z1290qCBDA=w2400" caption="Spring Bean Creation Lifecycle" %}

1. Instanciate Bean
2. **Spring IoC container add metadata to the bean**
3. If `BeanNameAware` is implemented, Spring will set the name pass as parameter in `setBeanName()`.
4. If `BeanFactoryAware` is implemented, Spring will call `setBeanFactory()`.
5. If `ApplicationContextAware` is implemented, Spring will pass a reference to the `ApplicationContext` in `setApplicationContext()` method.
6. If `BeanPostProcessor` is implemented, it will run `ProcessBeforeInitialization()` method.
7. If `@PostConstruct` is used, `InitializingBean` is implemented, or `init()` is implicitly called, Spring calls `afterPropertiesSet()` method.
8. If `BeanPostProcessor` is implemented, Spring will run `postProcessAfterInitialization()` method.
9. The bean now can be used and remains in the application context until it is destroyed.


### Spring Bean Destruction Lifecycle

1. **Spring IoC container is shutdown**.
2. If `@PreDestroy` is used, `DisposableBean` is implemented, or `destroy()` method is implicitly called, Spring will run `destroy()` method.

{% include figure.html image="https://lh3.googleusercontent.com/_zltXkTlijU1wZpQL5WvH1r82nMEmekAavT3O_nPC1xBVaoxv-hQmcv3fxeiBW7HLArDWfTK5CQqVeXQNiqDeWFVU2chCbhnbL2uggQsjZgiTueQDJRRU2d_WBL4wDuPQcaPxTvHXA=w2400" caption="Spring Bean Destruction Lifecycle" %}

### Lifecycle Callbacks

Spring recommend us to use @PostConstruct and @PreDestroy annotations to perform work during bean initialization and bean destruction.

>In case of XML configuration we can use the init-method or destroy-method attributes.

In the following Bean declaration we are combining Lifecycle mechanisms, and each one will have a different precedence.

```java
@Component
public class ExampleBean implements InitializingBean, DisposableBean {

    @PostConstruct
    public void callbackInitMethod() {
        System.out.println("Call init method from Annotation");
    }

    @PreDestroy
    public void callbackDestroyMethod() {
        System.out.println("Call destroy method from Annotation");
    }

    // Not recommended! It couples the code to Spring.
    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("Call init method from afterPropertiesSet");
    }

    // Not recommended! It couples the code to Spring.
    @Override
    public void destroy() throws Exception {
        System.out.println("Call destroy method from destroy implementation");
    }

}
```

When running the application we can see when these methods are called.

```shell
2019-04-16 18:07:16.638  INFO 25181 --- [           main] c.s.s.SpringBeanLifecycleApplication     : Starting SpringBeanLifecycleApplication on LONC02WW2Q9JG5L with PID 25181
2019-04-16 18:07:16.640  INFO 25181 --- [           main] c.s.s.SpringBeanLifecycleApplication     : No active profile set, falling back to default profiles: default
Call init method from Annotation
Call init method from afterPropertiesSet
2019-04-16 18:07:16.925  INFO 25181 --- [           main] c.s.s.SpringBeanLifecycleApplication     : Started SpringBeanLifecycleApplication in 0.453 seconds (JVM running for 0.845)
Call destroy method from Annotation
Call destroy method from destroy
```

As we showed before, the annotations are executed first, and then the overriden methods.

Spring also provides startup and shutdown callbacks and in order to be able to use them, we just need to implement the Lifecycle interface. Additionally, we can implement LifecycleProcessor to react when the context changes through onRefresh() and onClose(). Finally, in case we need a fine-grained control over the startup of a bean we can implement SmartLifecycle, so we can specify when the bean should start and stop, before or after other beans (the default value is 0).

```java
@Component
public class ExampleBean implements Lifecycle, LifecycleProcessor, SmartLifecycle {
    
    @Override
    public void start() {
        System.out.println("Bean is starting");
    }

    @Override
    public void stop() {
        System.out.println("Bean is stopping");
    }

    @Override
    public boolean isRunning() {
        return false;
    }

    @Override
    public void onRefresh() {
        System.out.println("Context is refreshed");
    }

    @Override
    public void onClose() {
        System.out.println("Context is closed");
    }
}
```