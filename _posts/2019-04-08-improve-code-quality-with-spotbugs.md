---
title: Improve Code Quality with SpotBugs
tags: [Java, Code Quality, Performance]
style: fill
color: primary
description: Either you are new in Java or want to make sure you are not introducing new bugs, learn how to use SpotBugs.
---

{%- capture list_items -%}
Introduction
SpotBugs
What it is
How To Use
Advance Configuration
Alternatives
Conclusion
{%- endcapture -%}

{% include list.html title="Content" type="toc" %}

## Introduction

Every developer should ensure code quality and follow language conventions, otherwise [Technical Debt](https://medium.com/existek/what-is-technical-debt-and-how-to-calculate-it-80193e4e746d) is created, and at some point in the future you will have to revisit that smelly piece of code.

**Code Debt** can be created without even realizing when: development has to be done before a deadline; you do not have enough experience; or simply you are having a bad day. Because of this, it is very important that before merging changes into master we double check that we are not introducing performance or security issues, or any other kind of code smell.

## SpotBugs

### What it is

- [SpotBugs](https://spotbugs.github.io/) helps you analize your **Java** code to find bugs.
- Free open source tool which was previously named [FindBugs](http://findbugs.sourceforge.net/).
- It is a plugin available for Maven, Gradle, Eclipse, Ant...
- It looks at your source code and runs a static analysis.

### How To Use It

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
mvn clean install spotbugs:spotbugs
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

A filter file is a _XML_ file with a parent FindBugsFilter tag which contains as many `Match` tags as you want, and each `Match` element accepts many [types of Match clauses](https://spotbugs.readthedocs.io/en/stable/filter.html#types-of-match-clauses). Moreover, you can use [Java regular expressions](https://docs.oracle.com/javase/8/docs/api/java/util/regex/Pattern.html) to include/exclude classes, methods, fields or sources.

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
{% include button.html link="https://github.com/smartinrub/spotbugs-example" text="Code Examples" style="dark" %}
</p>

### GUI

SpotBugs has a GUI out-of-the-box by simply running

```shell
mvn spotbugs:gui
```

> A build and SpotBugs analysis is required

A new window will pop up with a bug tree which gives us the following information:
- Bug Category
- Bug Kind
- Bug Pattern
- Bug Rank

{% include figure.html image="https://lh3.googleusercontent.com/ANLKz0dfuvd1m0fKUhuUkBCGg5mBbObv0li-eiqbHiXeJpdy5a9N8LfJE5KZY0t0KtKjcb4_FntTYwYCsVUiKFQBVUOdGIPIppwLGokGYtoyhIcsda3J8BURIlDj3XCNao7L8uZm=w2400" caption="SpotBugs GUI - Hierarky" %}

When a particular bug is selected we will see a pane with the class and highlighted line code, and at the botton a description and possible solution.

{% include figure.html image="https://lh3.googleusercontent.com/_Tr_pt9VWpFMnJFYqwTPncFcaXofAyao5qpfG1veW6pEC0h9pzSDwneraWbY1hF3DaiE2Hb6_7xvnswG6lfoPXNHkp2GjJqtTXfcOg_dhaDOjANqkuYqBcmJGbAT1vIpt3ChiyIY=w2400" caption="SpotBugs GUI - Source Code and Description Panel" %}

You can also save the report and import or export filters.

> The GUI can also be run by itself and load the jar file that we want to analyze.

## Alternatives

Probably you have already heard about [**SonarQube**](https://www.sonarqube.org/), which basically provides SpotBugs features and a few extra more. In fact, _SonarQube_ used to use _FindBugs_ plugin to generate bug reports, until they decided to used their own analyzer and stop using **[Checkstyle](http://checkstyle.sourceforge.net/)**, **[PMD](https://pmd.github.io/)** and **FindBugs**. 

**Why would you choose SpotBugs?** Because it is easier to integrate into your Maven build, rather than relying on a separate Sonar server, and having to learn an additional _API_. The greatest benefit of **SonarQube** is the GUI, which lets you configure anything easily. Nevertheless, you could run something like [Jenkins Warnings Next Generation Plugin](https://github.com/jenkinsci/warnings-ng-plugin) as part of your **Jenkins CI build** and have nice graphs.

## Conclusion

Whether you are a junior or senior developer, code analysis tools like Spotbugs can be very useful to improve code quality and avoid bugs, so you should consider adding this tool to your build.
