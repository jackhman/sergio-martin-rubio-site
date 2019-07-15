---
title: Behavioral Patterns Features
tags: [Java, Design Pattern]
style: fill
color: primary
description: Behavioral design patterns are use to improve communication and flexibility between objects 
---

{%- capture list_items -%}
Structural Patterns Features
Observer Pattern
Command Pattern
Strategy Pattern
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

## Structural Patterns Features

 Name | Intent | Motivation | Applicability | Consequences | Implementation
---------|----------|---------|----------|---------|----------|---------
**Observer** | It Notifies a set of listeners when there is a change in a particular place of the system | It used when changes frequently happen and one or more than one objects need to be notified | This pattern is used to reflect changes in an object where there are changes in another object, without having these two object coupled. This pattern allows us to add additional observers in the future with minimal changes | If we have many observers and observables, the communication between them can become complex | An Observable object needs to be created and then the observers subscribe to the observable. Each time the observable state changes, attached observers are notified 
**Command** | The Command Pattern allows you to create a set of behaviours for a particular subject | It used to queue commands and keep a history of them to do and undo actions | Do and undo actions; Transactional behaviours; Store action to be execute later;To create configuration input | It allows you to decouple the object that invokes the action from the object that performs the action. Make sure that the invoker in only aware of command class | The client will ask for a command to be executed and there is an invoker which takes the command and put it in a queue, and finally when the command is executed by a concrete command implementation, the result is sent to a Receiver.
**Strategy** | Defines a family of behaviors which are interchangeable | The strategy pattern allows you to change at runtime the way an object works. Therefore, instead of having multiple instances, there will be only one that is able to interchange different behaviors, and the only requirement for these strategies or behaviors is having a common interface required by the context object | This pattern can be used to apply different mathematical operation on the fly by using the same instance of the context | It changes the behavior of an object on the fly, and satisfies Open-Closed SOLID principle and allows you to program by interface, and not by implementation. This means that the context is not tightly coupled to classes and can receive any object that implements a particular interface | Each strategy must implement the same interface required by the context. The context injects a Strategy interface so it will contain a reference to a concrete strategy and by doing this the client can inject different concrete strategies at runtime. It is very important that the client understands the strategies available. It is also possible that some fields may not be used by the concrete strategy

## Observer Pattern

{% include elements/figure.html image="https://lh3.googleusercontent.com/obfvPJdabip5sx9d-P3SvrPZiZmdgX_wcIm1r9Xuyj29gGB6E3HthjAAEqwQrGvh92Da5wS2htC23uH3vM6E_lwAYwfLfvH9lJuxHt_HpDuvT1KNeu3MxM91Md0OtNPQexzrfFZQNQ=w2400" caption="Observer Pattern - UML" %}

### Use Cases

- MVC: the Observer Pattern is used in the Model View Controller architecture. This pattern is used to separate the model from the view, where the view is the observer and the model is the observable.
- UI: Swing or Android Listeners make extensively use of this pattern in order to get events and display changes.

<p class="text-center">
{% include elements/button.html link="https://github.com/smartinrub/java-observer-pattern" text="Source Code" %}
</p>

## Command Pattern

{% include elements/figure.html image="https://lh3.googleusercontent.com/JJ1ZiakDWMEFaipz1uV9HT30a8psUDEnM4X84v0_uqRPqz_sQYyVhXY2tq5T2BbKaGIr6-0mizA3XKhBsr6gu8mGZJp_wsxWRXr6qYVWkM2zmtTCl1R2ccSGYDC8klByVYpMqvvXiA=w2400" caption="Command Pattern - UML" %}

### Use Cases

- Undo and redo actions, since it keeps a history of commands and we can go back and forward as many times as we want.

<p class="text-center">
{% include elements/button.html link="https://github.com/smartinrub/java-command-pattern" text="Source Code" %}
</p>

## Strategy Pattern

{% include elements/figure.html image="https://lh3.googleusercontent.com/Df1Mtbk97ZIaV3yjQFscOnOn9Pcfl74gUnbCG7fxyrQPVHPN27dn6OW_naO8QuI75zxq3xz6dH4rGxZ9iVmzxv7302NQwT3LsnEJ0yBcQyT-cFt4jaQCdX9DKWye-2v3HPxkZ_hnuQ=w2400" caption="Strategy Pattern - UML" %}

### Use Cases

- Apply different kind of encryption.
- Multiple data compression formats.
- Custom sorting strategies.

<p class="text-center">
{% include elements/button.html link="https://github.com/smartinrub/java-strategy-pattern" text="Source Code" %}
</p>
