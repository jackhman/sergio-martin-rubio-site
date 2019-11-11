---
title: Approaches when you cannot return a value since Java 8
tags: [Java]
style: fill
color: primary
description: You don't know what to return from your Java method? You can find here some advices!
---

{%- capture list_items -%}
Introduction
Optionals
Drawbacks
Conclusion
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

# Introduction

Before **Java 8** there were only two possible ways to return something when a value was not available:

- _**null**_: efficient, but risky (we have to make sure that the client is going to handle a possible null return).
- _**Exception**_: elegant, but expensive (the stack trace is captured).

# Optionals

Since **Java 8** we have a third alternative, **Optionals**, which can contain an element or nothing. **Optional** allows us to return an empty result.

The optional library provides two ways to return an empty Optional: `Optional.empty()` and `Optional.ofNullable(nullValue)`. But do not use `Optional.of(value)` if a null value is expected to be returned, because in that case it will throw a `NullPointerException`.

```java
public class MyClass {

	public static Optional<Integer> findMyLuckyNumber(List<Integer> list, Integer luckyNumber) {
		
		if (list.isEmpty()) {
			return Optional.empty();
		}
		
		Integer result = null;
		
		for (Integer value : list) {
			if (value == luckyNumber ) {
				result = value;
			}
		}
		
		return Optional.ofNullable(result);
	}
}
```

```java
private static final Logger LOGGER = Logger.getLogger(Main.class.getName());

public static void main(String[] args) {
	
	// returns Optional with value
	LOGGER.info(MyClass.findMyLuckyNumber(List.of(2, 5, 9), 9).toString());
	// returns empty Optional
	LOGGER.info(MyClass.findMyLuckyNumber(List.of(2, 5, 9), 1).toString());
	// returns empty Optional
	LOGGER.info(MyClass.findMyLuckyNumber(new ArrayList<>(), 1).toString());
}
```

**Should I always used Optional instead of returning null or throwing an exception?** In most of the cases it is recommended to return an _**Optional**_, because returning null or throwing an unchecked exception might be ignored by the client, and returning a checked exception forces the client side to write more code. Therefore, if an _Optional_ is returned the client can decide what to do.

A default behaviour can also be specified when null is returned by appending `.orElse(“Empty…”)` or `orElseThrow(Exception::new)`. Another alternative is to use `.orElseGet(Supplier)`, which is more efficient than `.orElse()`, because the value is only retrieved when necessary. Remember that all these options return unwrapped values.

**Java 9** added three new methods into the _Optional_ class: `.or(Supplier<T>)`, `ifPresentOrElse(Consumer, Runnable)` and the stream method, which allows us to use _Optionals_ as streams. The main advantage of the first two methods is that now we can return an _Optional_ as default value.

```java
Optional<Integer> defaultValue = Optional.of(99);
Optional<Integer> emptyValue = Optional.ofNullable(null);
// prints 99
System.out.println(emptyValue.or(() -> defaultValue).get());
```

You can also check if a _Optional_ is empty by calling `isPresent()`, but since we have the methods mentioned above, you would rather use those for simplicity.

# Drawbacks

But returning _Optional_ is not always the best solution. For those cases when a list needs to be returned, it is better to just return an empty list. Another drawback of _Optional_ is the cost of use it, since Optionals require to be allocated and initialized. Moreover, for primitives values Optional is not suitable, instead you have to use `OptionalInt`, `OptionalLong` or `OptionalDouble`. Finally, you also should not use _Optionals_ as keys, values or elements in collections or arrays.

# Conclusion

- **Return _null_ or _Exception_ when performance is priority**.
- **Only use _Optional_ as a return value**.
- **_Optional_ fields are sometimes considered as _code smell_**, because maybe there should be a subclass containing the optional values.