---
title: Initialise fields wisely in Java
tags: [Java]
style: fill
color: primary
description: Depending on how you initialize your variable it may impact on performance. Have a look at this article to learn more about this interesting topic!
---

## Introduction

Mainly there **two ways of initialize a field**, **eager** and **lazy** initalization. On the one hand, lazy initialization consists of initializing the field when the value is needed, whereas the eager option is base on initializing the variable when it is declared.

Both choices are compatible with _static_ and _instance fields_, however the implementation may differ when trying to achieve performance improvement.

## Eager Initialization

Eager initialisation is commonly used when:

- **Performance is not a priority**.
- **Not expensive to initialise**.
- **High number of accesses inside a class**.
- **Many threads**.

```java
private final Object eagerObject = new Object();
```

## Lazy Initialization

Lazy initialisation is preferable when:

- **Improve performance**.
- **Reduced access from instances of a class**.
- **Costly to initialise**.
- **Not many threads** (when two o more threads, _synchronisation_ might be required).

```java
private Object lazyObject;

private synchronized Object getLazyObject() {

    if (lazyObject == null)

        lazyObject = new Object();

    return lazyObject;
}
```

## Use Cases

As you can see it is really important to know the context of the field and how it is going to be accessed to decide what kind of initialisation is preferred. However, bear in mind that most of the times eager initialisation is preferable.

When **lazy initialisation** and **static** fields are combined the lazy initialisation holder class idiom is recommended.

```java
private static class OjbectHolder {

    static final Object object = new Object();
}

private static Object getField() { return OjbectHolder.object; }
```

By doing this, the static object is going to be initialise only the first time `getField()` is called, since it access ObjectHolder.object and the object gets initialised. In addition, the `getField()` method is not synchronised and it is only a field access, therefore there is no cost of access.

In case you want to use lazy initialisation for an instance field use the [Double Check Locking Pattern](http://www.java67.com/2016/04/why-double-checked-locking-was-broken-before-java5.html).

```java
private volatile Object lazyIntanceObject;

private Object getLazyIntanceObject() {

    Object result = lazyIntanceObject;

    if (result == null) {  // 1st check

        synchronized(this) {

            if (lazyIntanceObject == null)  // 2nd check (locking thread)
                lazyIntanceObject = result = new Object();
        }
    }
    return result;
}
```

The previous code snipped might look complicated and it is indeed.

- A **volatile** field is created to hold the object. The variable is stored in the main memory, so reading or writing to the _volatile_ variable will be on the main memory and not only on the _CPU_ cache. This ensured that the field will never be half initialised.
- The new variable is created to ensure that the field is read only.
- The first check is to improve performance by verifying if the field is already initialised before locking and allowing that multiple threads can access to the field.
- The synchronised block ensures that only one thread can go inside the block, checks if the field is already initialised (it might happen there is a [race condition](http://tutorials.jenkov.com/java-concurrency/race-conditions-and-critical-sections.html)), and if not, initialises the object.

## Conclusion

You should choose eager initialisation over the lazy one in most cases. Only use lazy initialisation when the performance is a priority or in order to break _initialisation circularity_.