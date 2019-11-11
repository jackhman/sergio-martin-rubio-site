---
title: First Steps in JMeter
tags: [Java, Performance, Testing]
style: fill
color: warning
description: Want to know more about performance testing? Then, have a look at how at this article where we explain how to use.
---

{%- capture list_items -%}
Introduction
JMeter
Installation and execution
Elements of a Test Plan
Running Test Plan
Conclusion
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

### Introduction

In this post we are going to go through the main **JMeter** features, but first of all, what is [JMeter](https://jmeter.apache.org/)? basically, it is a tool to perform load testing against:

- Web – _HTTP, HTTPS APIs_
- Webservices (_SOAP_ or _REST_)
- _FTP_
- Database via _JDBC_
- _LDAP_
- _JMS_ services
- Mail
- Native commands or shell scripts
- _TCP_
- _Java Objects_

However, we will only cover the HTTP functionalities of **JMeter**.

Performance tests are required to make sure your application will not crash under heavy traffic, and survive dates such as Black Friday or Christmas. Therefore, it is a very important phase of the web development process.

**JMeter** is an open source and Java based that provides an _GUI_ to create your test plans, and simulates browser behaviour by sending customised request to our webiste.

### JMeter

#### Installation and execution

##### Ubuntu 18

1. Download binaries from **Apache JMeter**.
2. Unzip file:

```shell
tar -xf apache-jmeter-5.0.tgz
```

3. Run executable file:

```shell
cd apache-jmeter-5.0/bin
./jmeter
```

##### MAC OS

1. Install it with **brew**

```shell
brew install jmeter
```

2. jmeter should be on your PATH

```shell
jmeter
```

#### Elements of a Test Plan
#### Test Plan

This parent object will contain all the necessary components to build your test plan.

##### Thread Group

**Thread Group** is the first element of your test plan and all samples, controllers and listeners are meant to be under this object. However, listeners are allowed to be under the Test Plan object, and in this case they are applied to all Thread Groups. What can you do from a Thread Group?

- Set number of threads or users.
- Set the time for **JMeter** to run all threads, or in other words, it is the time needed by JMeter to add all users (threads) to the test execution. If you set this value to 100 and the number of users is 10, it will add an user every 10 seconds.
- Set how many times a _Thread Group_ will execute.
- Other options: scheduler, delays, actions after error…

##### Controllers

**_Sampler_**

This type of controller let you send different types of requests (e.g. _HTTP, FTP, JDBC_…).

The _HTTP Request_ controller is used to send requests to the web server.

If you are planning to send multiple request to the same server it is recommended to use Config Element components for your Thread Group, so you can set default request values:

-  **Defaults** allows you to specify default values for your HTTP calls (e.g. domain, port, parameters, body…) inside a Thread Group or for the whole Test Plan.
- **HTTP Header Manager**: allows you add or override HTTP request headers.
- **HTTP Cookie Manager**: stores and sends cookies like a web browser. It is very useful to keep the same session in a Thread Group.
- **HTTP Cache Manager**: To simulate browser cache features and with the default **JVM** value, the limit is 5000 items.
- **HTTP Authorization Manager**: for web pages with restricted access.

Another interesting option to generate test samplers automatically is [HTTP(S) Test Script Recorder](https://jmeter.apache.org/usermanual/component_reference.html#HTTP(S)_Test_Script_Recorder).

_**Logic Controller**_

We can use these controller to specified how and when to run the samplers. The most relevant types are:

- **Simple Controller**: for onganization purposes only.
- **Loop Controller**: To loop through an object a particular number of times. If you set the value to -1 it will loop forever.
- **Once Only Controller**: runs what is inside once per thread.
- **Interleave Controller**: alternate among each controller. If you use it with a Loop Controller it will run a different controller for each iteration.
- **Random Controller**: it is like the interleave controller but without an order.
- **Random Order Controller**: it is like Simple controller but without an order.
- **Throughput Controller**: it controls how often a controller is executed.
- **Runtime Controller**: it sets for how long the children controllers will run.
- **Recording Controller**: it is a placeholder to use in combination with HTTP(S) Test Script Recorder.

##### Listeners

Listeners are essential to retrieve the results from your tests and analyze the Sampler requests and responses by looking at tables, graphs, trees, statistics… Moreover, they allow you to save the results in csv or xml files, so you can have a look at them later.

Some of the most relevant listeners are:

- **View Results Tree**: this listeners shows requests and responses to see if something went wrong during the calls. Since it consumes a lot of resources, it is only recommended to use for debugging purposes.

{% include elements/figure.html image="https://lh3.googleusercontent.com/u_v6Qrm-B4udK88lY1kZmbmPqH5b7VDJFf_lK8qWQqqKEHP3MEnRPE6FowK22DeqGwOKafTql-8lxN82i_7cP8APG1Lm27Od9ctlOaKgW2SgRoY_u24p4f6_ZLmq-a5xUZ5gFLcO=w2400" caption="JMeter - Results Tree" %}

- **Aggregate Report**: it gives you some statistics parameters (average, median, min, max, 90th percentile…) in form of a table.

{% include elements/figure.html image="https://lh3.googleusercontent.com/T5PuPN_vbh1_8fAEerkeJ9U9vqqY6sSnV2ymdD9xhj6yt73ZZmrzvdGt6ouxMqlb4WxoaCLCYjQD0-RKjKZJwRZTLoHoPxaPOKTu64sECW0O1RF3hNK85S8iL9dGvNiJ19wFrBP-=w2400" caption="JMeter - Aggreate Report" %}

- **View Results in Table**: it creates a row for every sample result, so you can see in depth what is happening during each result. Bear in mind that this listener also consumes a lot of memory.

{% include elements/figure.html image="https://lh3.googleusercontent.com/YQoPGodxuxrR2oASmwutuYi3PPyShOLvTQ4oeWFbM1h9_RNvEIomH5RI6rH9fixCHKHY1AF1d5ZFHjvxmsh7swrB3B1TFbYMLlGjD378z0nwx7moG-WJMBFEc9RRxdbRFcmivLxM=w2400" caption="JMeter - View Results" %}

- **Simple Data Writer**: this is the most performance listeners, since the only thing it does it is write the metrics in a external file.

{% include elements/figure.html image="https://lh3.googleusercontent.com/M4lMFtgCsby8bUbn13ajQP21_e337vMV5U6tWR8O8lMMfzbcu9yMeqi1A-pg3Rg44w8TFRvWrBCgsoxEws0AzuGsP_pAb6QKYCCBSNV5D6tOyfMlGb6BW2uxyWrV-_4ewbrs7m3L=w2400" caption="JMeter - Data Writer" %}

- **Backend Listener**: you can even push data to your Graphite server. You can try this listener by running a dockerized version of Graphite.

{% include elements/figure.html image="https://lh3.googleusercontent.com/YcEchTIoon604za-_lyi46qa_PL5QgqpDlWD4x1K6w9YZgeO-ygEPv90u_F3NxY62_RyXnzrr_C19e2xnLpxOS7fIcLaxCsfcZed_CkPBWNqY9KLApb4j7BtP5CxhEQJ_Sp7Sj8P=w2400" caption="JMeter - Listener" %}

{% include elements/figure.html image="https://lh3.googleusercontent.com/aJCQJxCmuZHXJ752dD1ShhmQ1O6susVSbZL7lRvC2qQpDDw9d8K6ANTPj7vcEGs2OokC5ez1ruxTiRId9buFdyNzlpr45r4zv-eSSojB3Hg1tJfKf8SYSwJxrbg7XsWWadEZnG-f=w2400" caption="Graphite Dashboard" %}

In order to save these reports into **CSV** files you only need give a path a filename. For **XML** output Save as XML option needs to be selected in Configure.

##### Timers

**Timers** add delays to every requests of your **Thread Group** to avoid overwhelming the server. If you want to apply a timer to only one element, add the timer as a child of it. Some of the common timers are:

- **Constant Timer**: it delays each call for the same amount of time.
- **Uniform Random Timer**: it delays each request for a random amount of time.
- **Constant Throughput Timer**: this timer uses variable delay for each request base on the server performance, so what you set on this timer is the number of samples per minute.

##### Assertions

**Assertions** are very useful to make sure for instance you are getting the expected response.

The most relevant assertion for HTTP requests is _Response Assertion_. This assertion allows you to set patterns for _Request and Response Headers_, _Request Data_ or _Response Message_.

{% include elements/figure.html image="https://lh3.googleusercontent.com/p1Vv4dj72ETMp9WgjB-1p_bkZesmDZRhY0GXIoKcplZkEy1h-qDbUB6sCzR2s7Wk2FoL5-AOP9ykvASy1E3L0HOUafPfq7r978AFOOiAtDWwEuE3DQdJX8fVUYQF1O29Kzl2ynVB=w2400" caption="JMeter - Response Assertion" %}

##### Templates
**JMeter** includes some templates to get started. Building an **Advanced Web Test Plan** is one of them and includes many of the elements described before (config elements, controllers, assertions, listeners, timers…).

{% include elements/figure.html image="https://lh3.googleusercontent.com/e-xiY2-yPEOh1jWAKSJQmudPzZflCFbbFrNOrjm8uWcyA2eEuG2G7BUXMAJPIOYIfVG9FIYR23e-RpFLGYclYdJ9EgD8GGluyiX8X1HUZK6VawlevnVqzfsdDQXA0u_vKlDGY-u2=w2400" caption="JMeter - Left Pane" %}

#### Running Test Plan

There are two options to run your test plan. You can use the _GUI_ provided by _JMeter_, however this alternative is not recommended, since JMeter consumes much computer memory, and it should only be used for creating the test script. On the other hand, non _GUI_ is much more lightweight and should be used for running the test plan.

```shell
jmeter -n -t test_plan.jmx -l results.csv -j jmeter_logs.log
```

-n  non-gui mode

-t  Test Plan file

-l   file to save results (csv, jtl)

-j  JMeter log file

### Conclusion

**JMeter** is a powerful and customizable tool for load testing, and provides a friendly user interface to create your scripts. However, it is a heavyweight tool compare to other load testing utilities like Gatling or Locust.