---
title: Creational Patterns Features
tags: [Java, Desgign Pattern]
style: fill
color: primary
description: Creational design patterns provide object creation mechanisms, which increase flexibility and code reusability
---

{%- capture list_items -%}
Creational Patterns Features
Singleton Pattern
Factory Pattern
{%- endcapture -%}

## Creational Patterns Features

 Name | Intent | Motivation | Applicability | Consequences | Implementation
---------|----------|---------|----------|---------|----------|---------
 **Singleton** | It creates a single instance of a class for the entire lifespan of an application | It used to make sure that an object is created only once | It can be applied to a logging class, because a logging class usually needs to be used over and over again by every class in a project. Another applicability it is when you need to configure a network connection, because it will always connect to the same host. | An instance of the singleton class is created when it is called the first time or when getIntance method is called, depending on the implementation. By making the constructor private you are not able to create more than one object of the same singleton class | |
 **Factory Method** | It creates an object without exposing the implementation logic | It provides a way to encapsulate object of the same type and avoid implementation details on the client side | It can be used to create different database connections. One of the connection object is returned and can be used to open the connection with a particular database | By delegating the creation of the Product to the Factory, clients does not need to know anything about how the Product is created. Therefore, this pattern allows you to separate or decouple your application, and introduces an easy way to customize the concrete products | When implementing this pattern you have to make sure that all products can use the same Product interface. This pattern is suitable when you have many objects of the same family or type and you manipulate them frequently |

## Singleton Pattern

{% include figure.html image="" caption="Singleton Pattern - UML" %}

Basically, there four ways to create a singleton class:

- Eager initialisation with private constructor and declaring a public final field.
- Eager initialisation with private constructor and creating a static factory method.
- Lazy initialisation and Double Check Locking pattern
- Declare a single element Enum.

### Singleton with public final field

```java
public class MySingleton {

    public static final MySingleton instance = new MySingleton();

    private int value;

    private MySingleton() {

    }

    public void setValue(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }
}
```

The main problem of this aproach it is that the constructor can be invoked reflectively. Therefore, there are ways to create more than one instantiation of the class.

```java
// reflection concept to get constructor of a Singleton class.
Constructor<MySingleton> constructor = MySingleton.class.getDeclaredConstructor();
// change the accessibility of constructor for outside a class object creation.
constructor.setAccessible(true);

// creates first object of a class as constructor is accessible now.
MySingleton firstSingleton = constructor.newInstance();
firstSingleton.setValue(5);

// creates second object of a class as constructor is accessible now.
MySingleton secondSingleton = constructor.newInstance();
secondSingleton.setValue(10);
System.out.println(secondSingleton.getValue()); // value = 10
System.out.println(firstSingleton.getValue()); // value = 5

constructor.setAccessible(false);
```

As you can see we are able to create more than one MySingleton object with different values.

One solution to avoid reflection would be throw an exception when calling the constructor.

```java
private MySingleton() {
    throw new RuntimeException("You are trying to call the constructor!");
}
```

### Singleton with factory method

```java
public class MySingleton {

    private static MySingleton instance = new MySingleton();

    private int value;

    private MySingleton() {

    }

    public static MySingleton getInstance(){
        return instance;
    }

    public void setValue(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }
}
```

**Advantages**:

- It is **more flexible.**
- Allows you to **create a generic singleton factory**.
- It is a supplier and allows you to call it as `MySingleton::getInstance`.

**Disadvantages**:

- Constructor can be invoked reflectively.
- If you are not going to take advantage of any of the previous points, it is recommended to use the first option.

### Lazy initialisation and Double Check Locking pattern

When you want to make a Singleton **thread-safe** you need to use **Double-Checked Locking Pattern**.

```java
public class MySingleton {
	private static volatile MySingleton instance;

	private MySingleton() {

	}

	public static MySingleton getInstance() {
		if (instance == null) { // 1st check

			synchronized (MySingleton.class) {

				if (instance == null) { // 2nd check
					instance = new MySingleton();
				}
			}
		}
		return instance;
	}
}
```

- A **_static volatile_** field is created to hold the instance. The variable is stored in the main memory, so reading or writing to the volatile variable will be on the main memory and not only on the CPU cache. This ensured that the singleton will never be half initialised.
- The first check is not _synchronised_ to improve performance and enable reordering of **_JVM_**.
- The second _synchronised_ check is executed only once during lifespan of Singleton. By doing this you get the performance boost because locking only happens once.

### Enum singleton (preferred way)

```java
public enum MySingleton {

    INSTANCE;

    private int value;

    public int getValue() {
        return value;
    }

    public void setValue(int value) {
        this.value = value;
    }
}
```

**Advantages**:

- **Protection against reflection**.
- Provides **Serialisation**. There is no way to create more than one instance of the Singleton.
- **Thread-safe** out-of-the-box.
- **More simple**.

**Disadvantages**:

- Your singleton **only can extend a Enum** superclass

### Why avoid overusing Singleton Pattern?

Singleton is a powerful pattern but use it only when it is strictly necessary.

- Singleton clients will be **harder to test** since it is not possible to substitute a mock implementation for a singleton class.
- This pattern may behave as an **anti-pattern**, because introduces global variables in your application.
- Singletons are bad when implemented with **multi-threading**.

### Use Cases

**Android Volley Library** to make _HTTP_ calls:

```java
public class MySingleton {
    private static MySingleton mInstance;
    private RequestQueue mRequestQueue;
    private ImageLoader mImageLoader;
    private static Context mCtx;

    private MySingleton(Context context) {
        mCtx = context;
        mRequestQueue = getRequestQueue();

        mImageLoader = new ImageLoader(mRequestQueue,
                new ImageLoader.ImageCache() {
            private final LruCache<String, Bitmap>
                    cache = new LruCache<String, Bitmap>(20);

            @Override
            public Bitmap getBitmap(String url) {
                return cache.get(url);
            }

            @Override
            public void putBitmap(String url, Bitmap bitmap) {
                cache.put(url, bitmap);
            }
        });
    }

    public static synchronized MySingleton getInstance(Context context) {
        if (mInstance == null) {
            mInstance = new MySingleton(context);
        }
        return mInstance;
    }

    public RequestQueue getRequestQueue() {
        if (mRequestQueue == null) {
            // getApplicationContext() is key, it keeps you from leaking the
            // Activity or BroadcastReceiver if someone passes one in.
            mRequestQueue = Volley.newRequestQueue(mCtx.getApplicationContext());
        }
        return mRequestQueue;
    }

    public <T> void addToRequestQueue(Request<T> req) {
        getRequestQueue().add(req);
    }

    public ImageLoader getImageLoader() {
        return mImageLoader;
    }
}
```

## Factory Pattern

{% include figure.html image="" caption="Factory Pattern - UML" %}

### Use Cases

[BorderFactory.createLineBorder](https://docs.oracle.com/javase/6/docs/api/javax/swing/BorderFactory.html) from **Java API** uses **Factory Pattern**, but in this case there is only one factory family, so no factory interface was created.

```java
public class BorderFactory
{
    private BorderFactory() {
    }
    public static Border createLineBorder(Color color) {
        return new LineBorder(color, 1);
    }
    public static Border createLineBorder(Color color, int thickness)  {
        return new LineBorder(color, thickness);
    }
    public static Border createLineBorder(Color color, int thickness, boolean rounded) {
        return new LineBorder(color, thickness, rounded);
    }
}
```

Learn more [here](http://coding-geek.com/design-pattern-factory-patterns/).

<p class="text-center">
{% include button.html link="https://github.com/smartinrub/java-factory-pattern" text="Source Code" %}
</p>
