---
title: Iteration vs. Recursion in Java
tags: [Java, Algorithm]
style: fill
color: primary
description: Want to learn more about using iteration and recursion in Java? Click here to learn more about these two methods and their strengths and weaknesses.
---

{%- capture list_items -%}
Introduction
Getting Started
Algorithms
Run JMH Benchmark
Conclusion
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

## Introduction

Iteration and recursion are exchangeable in most cases. In theory, every program can be rewritten to avoid iteration using recursion. However, it is important to know that when using one or the other, this decision might have severe impacts on performance or potentially raise unexpected errors.

## Getting Started
### Algorithms

- `for` loop: is the most traditional way to iterate an array or collection
- Recursion: cleaned and simplified way to achieve the same as iterations
- Tail recursion: an optimized version of recursion
- `stream` library: the functional perspective to iterate collections

The problem of calculating the factorial of a number is that it shows performance differences between iteration and recursion. **JMH** harness is used to conduct the test in a single thread with the following setup:

- Factorial of number: 20
- CPU: intel i5-5250U
- OS: Ubuntu 18

Both warm up and test iterations ran for 2 seconds, with two forks, three warmups, and three iterations. The code to be tested is:

* **`for` loop**:

```java
public long factorialForLoop(long number){
    long result = 1;
    for(; number > 0; number--){
        result *= number;
    }
    return result;
}

publiclongfactorialRecursive(long number){
    return number == 1 ? 1 : number * factorialRecursive(number - 1);
}
```

* **Recursion**:

```java
public long factorialRecursive(long number){
return number == 1 ? 1 : number * factorialRecursive(number - 1);
}
```

* **Tail recursion**:

```java
public long factorialTailRecursive(long num){
    returnfactorial(1, num);
}

public long factorial(long accumulator,long val){
    return val == 1 ? accumulator : factorial(accumulator * val, val - 1);
}
```

* **"Stream" Library**:

```java
public long factorialStream(long number){
    return LongStream.rangeClosed(1, number)
    .reduce(1,(n1, n2) -> n1 * n2);
}
```

### Run JMH Benchmark

Run **JMH** is quite simple. You only need to add some annotations and a couple of dependencies to the pom file. Then, you can run the jar file with maven.

```shell
mvn clean install
java -jar target/benchmar.jar
```

<p class="text-center">
{% include elements/button.html link="https://github.com/smartinrub/java-iterative-benchmark" text="Source Code" %}
</p>

As you can see, for loop is the winner, as expected, because of the simplicity of the operations done during the iteration. However, this does not mean that it is always the best choice. Loops might be problematic when dealing with data structures shared by the caller of a method. It mutates the state of the object, so this is not a side-effect free option. Another negative factor of loops is their readability. Therefore, in case we want to use immutable data objects and write a cleaner code, there are other options.

In terms of readability, the winner is the stream. The functional style allows to write the iteration in a really simple manner and is side-effect free. However, the performance is quite bad since it is four times slower than the for  loop. The next candidate is recursion, which is also side-effect free. Before Java 8 was released, recursion had been used frequently over loops to improve readability and problems, such as Fibonacci, factorial, or Ackermann that make use of this technique. The main problem of recursion is the risk of receiving a  StackOverFlowError. This error happens because the accumulated result needs to be saved until the end of every call. The method pushes a new frame onto a thread's stack when it calls itself to keep all the intermediate operation, parameters, and variables. If the method keeps pushing frames for too long, the stack will exceed the limit and the  StackOverFlowError  will be thrown. But, there is an answer to this problem — tail recursion. Tail recursion avoids creating frames every time the method calls itself because the intermediate result is passed to the next call. Therefore, instead of accumulating frames, each frame can be reused. The bad news is that the compiler needs to be smart enough to do this optimization, and, at the moment, this is not supported by the JVM. However, I experienced a slightly better result when using tail recursion instead of recursion.

## Conclusion

If performance is the priority, traditional loops are the way to go. In regards to readability and immutability, if these are top priority, streams  are the best option. Lastly, if you are looking for something in between, recursion offers a good performance and is side-effect free.
