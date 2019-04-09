---
title: Creational Patterns In Action
tags: [Java]
style: fill
color: primary
description: Creational Patterns Features
---

## Creational Patterns Features

 Name | Intent | Motivation | Applicability | Structure | Participants | Consequences
---------|----------|---------|----------|---------|----------|---------
 Singleton | It creates a single instance of a class for the entire lifespan of an application | It used to make sure that an object is created only once | It can be applied to a logging class, because a logging class usually needs to be used over and over again by every class in a project. Another applicability it is when you need to configure a network connection, because it will always connect to the same host. | | Singleton class: The singleton class has an instance of itself. | An instance of the singleton class is created when it is called the first time or when getIntance method is called, depending on the implementation. By making the constructor private you are not able to create more than one object of the same singleton class.
