---
title: Improve your Code Quality with SpotBugs
tags: [Java, Code Quality, Performance]
style: border
color: primary
description: Either you are new in Java or want to make sure you are not introducing new bugs, learn how to use SpotBugs.
---

{%- capture list_items -%}
Introduction
SpotBugs
How To Use
Advance Configuration
{%- endcapture -%}

{% include list.html title="Content" type="toc" %}

## Introduction

Every developer should ensure code quality and follow language conventions, otherwise [Technical Debt](https://medium.com/existek/what-is-technical-debt-and-how-to-calculate-it-80193e4e746d) is created, and at some point in the future you will have to revisit that smelly piece of code.

**Code Debt** can be created without even realizing when: development has to be done before a deadline; you do not have enough experience; or simply you are having a bad day. Because of this, it is very imporant that before merging changes into master we double check that we are not introducing performance or security issues, or any other kind of code smell.

## SpotBugs

[SpotBugs](https://spotbugs.github.io/) helps you analize your **Java** code to find bugs. This is a free open source tool which was previously named [FindBugs](http://findbugs.sourceforge.net/).

### How To Use

There are a few ways of using **SpotBugs**, however, we are going to focus on the SpotBugs plugin for Maven. Alternatevely, you can execute SpotBugs on _Windows_, _macOS_ or _Linux_ to run the _SpotBugs GUI_; install a plugin for Eclipse; integrate it with **Ant** or add a **Gradle Plugin**.

1. Add the SpotBugs plugin into your pom.xml file:

```xml
<plugin>
  <groupId>com.github.spotbugs</groupId>
  <artifactId>spotbugs-maven-plugin</artifactId>
  <version>3.1.11</version>
  <dependencies>
    <dependency>
      <groupId>com.github.spotbugs</groupId>
      <artifactId>spotbugs</artifactId>
      <version>4.0.0-beta1</version>
    </dependency>
  </dependencies>
</plugin>
```

2. Run one of the goals provided by the plugin:

- **spotbugs** goal: runs analysis
- **check** goal: runs analisys and fails build if bugs are found
- **gui** goal: launches GUI to see results
- **help** goal: shows how to use SpotBugs plugin

```shell
mvn clean install spotbugs:gui
```

### Advance Configuration

Aditionally, we can know more about one particular goal if we run

```shell
mvn spotbugs:help -Ddetail=true -Dgoal=check
```

and set configuration properties like

```shell
mvn clean install spotbugs:check -Dspotbugs.failOnError=false
```

or in a configuratio block inside the **SpotBugs Maven plugin** block like below

```xml
<configuration>
    <threshold>Low</threshold>
    <effort>Max</effort>
    <debug>true</debug>
</configuration>
```

SpotBugs also allows advance [filter configuration](https://spotbugs.readthedocs.io/en/stable/filter.html#) in order to include or exclude specific classes or methods from the report. First, we need to create a xml file where we are going to define our _Match_ elements.

```shell
cd your-project-root-folder
touch spotbugs.xml
```

and add it in your SpotBugs plugin configuration

```xml
<includeFilterFile>spotbugs.xml</includeFilterFile>
```
A filter file is a _XML_ file with a parent FindBugsFilter tag which contains as many `Match` tags as you want, and each `Match` element accepts many ![types of Match clauses](https://spotbugs.readthedocs.io/en/stable/filter.html#types-of-match-clauses). Moreover, you can use [Java regular expressions](https://docs.oracle.com/javase/8/docs/api/java/util/regex/Pattern.html) to include/exclude classes, methods, fields or sources.

An example with some of the SpotBugs clauses can be found below.

```xml
<FindBugsFilter>
    <Match>
        <Class name="com.sergiomartinrubio.spotbugsexample.PerformanceBugs" />
        <Bug category="PERFORMANCE" />
    </Match>
    <Match>
        <Class name="com.sergiomartinrubio.spotbugsexample.CorrectnessBugs" />
        <Not>
            <Method name="test" />
        </Not>
    </Match>
    <Match>
        <Class name="com.sergiomartinrubio.spotbugsexample.BadPracticeBugs" />
        <Or>
            <Method name="removeAllFromCollection" returns="void" />
            <Method name="NamingConvention" returns="void" />
        </Or>
        <Bug category="BAD_PRACTICE" />
    </Match>

    <Match>
        <Package name="~.*\.spotbugsexample" />
        <Bug pattern="UCF_USELESS_CONTROL_FLOW" />
    </Match>
</FindBugsFilter>
```

<p class="text-center">
{% include button.html link="https://github.com/smartinrub/spotbugs-example" text="Code Examples" style="btn-dark" %}
</p>





