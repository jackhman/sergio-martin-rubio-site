---
title: Get Started With Mockito
tags: [Testing, Java, Mockito]
style: fill
color: warning
description: Mockito is a popular mocking framework for Java application. This framework helps you to test classes in isolation and avoids creating collaborators in your unit tests.
---

{%- capture list_items -%}
Introduction
What is a mock?
Mockito Setup
Create a Mock
Stub Methods
Void Methods
Argument Matchers
Verify Behaviour
Using Spies
Dependency Injection
Reset Mocks
Behavior Driven Development
{%- endcapture -%}

{% include elements/list.html title="Table of Contents" type="toc" %}

## Introduction

[Mockito](https://site.mockito.org) is a popular mocking framework for **Java** application. This framework helps you to test classes in isolation and avoids creating collaborators in your unit tests. This need is very common in a web applications that uses the MVC pattern. For instance you might need to test your service layer which uses an utility class to check the payment method and then will call an API to process the payment. In this situation you have to instanciate or inject your utility class and the API client, and the second one will required to start a mock server. As you can see this looks like an integration test instead of a simple unit test, and we are not testing the service layer in isolation becuase it requires that both the utility class and the API client are free of bugs.

## What is a mock?

A **mock** is a proxy to the original object, so if a method of a proxy object is called then the proxy object will decide what to do. When you do something like `Mockito.mock(Foo.class)` you are simply creating a proxy of object of the _Foo_ class.

Mockito uses [CGLIB](https://github.com/cglib/cglib) which is based on [ASM](https://asm.ow2.io). **CGLIB** generates proxy classes by extending the original class, and all the overriden methods will call [MethodInterceptor.intercept()](http://cglib.sourceforge.net/apidocs/net/sf/cglib/proxy/MethodInterceptor.html#intercept%28java.lang.Object,%20java.lang.reflect.Method,%20java.lang.Object[],%20net.sf.cglib.proxy.MethodProxy%29) if the proxy callback is not null otherwise the super method (original method) will be called. This means that proxy classes have some limitations: 

- Final classes cannot be mocked.
- Final, private or static methods cannot be intercepted.

>To create proxy classes of final classes you need to perform bytecode transformations. e.g. remove the `final` modifier.

>**AMS** is a framework to manipulate Java bytecode.

## Mockito Setup

You can start using mockito by adding the following Maven dependency:

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>2.28.2</version>
    <scope>test</scope>
</dependency>
```

If you use Gradle:

```groovy
repositories { jcenter() }
dependencies { testCompile "org.mockito:mockito-core:2.+" }
```

>Mockito is also included in `spring-boot-starter-test`.

## Create a Mock

You can use an static method or an annotation to create a mock.

```java
FooRepository fooRepository = Mockito.mock(FooRepository.class);
```

```java
@Mock
private FooRepository fooRepository;
```

`@Mock` annotation requires either `MockitoAnnotations.initMocks(testClass)` or `@ExtendWith(MockitoExtension.class)`. In JUnit 4 is `@RunWith(MockitoJUnitRunner.class)`.

```java
@Mock
private FooRepository fooRepository;

@InjectMocks
private FooServiceImpl fooService;

@BeforeEach
public void setUp() {
    MockitoAnnotations.initMocks(this);
}
```

```java
@ExtendWith(MockitoExtension.class)
class FooServiceTest {

    @Mock
    private FooRepository fooRepository;

    @InjectMocks
    private FooServiceImpl fooService;

    ...
}
```

## Stub Methods

Any mocking framework allows you to return the desired value when a particular method is called, and with Mockito this can be achieved with `Mockito.when()` in conbination with `thenReturn()`. This is what we call a stub.

```java
when(fooRepository.getFooMessage()).thenReturn("Hello");
```

How does is work? A proxy is dinamically defined, so when the static method `Mockito.when()` is called, the passed method is stored. Then, when `thenReturn()` is called the value is also stored to the saved method. Finally, if the same proxy method with the same parametes is called again it will return the stored value this time.

You can stub the same method multiple times:

```java
when(fooRepository.getNumber(0)).thenReturn(8);
when(fooRepository.getNumber(1)).thenThrow(new ArrayIndexOutOfBoundsException());
```

Mockito also has other static methods that can be combined with the `when()` method like the one used in the previous example `.thenThrow()`.


Method | Description
---------|----------
 `thenReturn(T value)` | Returns given value 
 `thenThrow(Throwable throwableType)` | Throws given exception 
 `thenAnswer(Answer<?> answer)` | It's use to return custom answers 
 `then(Answer<?> answer)` | Alias of `thenAnswer()`
 `thenCallRealMethod()` | The original method is called. It's a partcial mock.

 >`thenCallRealMethod()` cannot be used with mocked interfaces or abstract classes. No data from the original class is initialized.

 You can also stub multiple calls to the same method as follows:

```java
@Test
public void getNumberMultipleTimesTest() {
    // GIVEN
    when(fooRepository.getNumber(anyInt())).thenReturn(8, 3, 5);

    // WHEN
    Integer firstNumber = fooService.getNumber(0);
    Integer secondNumber = fooService.getNumber(1);
    Integer thirdNumber = fooService.getNumber(2);

    // THEN
    assertThat(firstNumber).isEqualTo(8);
    assertThat(secondNumber).isEqualTo(3);
    assertThat(thirdNumber).isEqualTo(5);
}
```

### Void Methods

Void methods require are different strategy from `when()`. Mockito provides a set of methods to stub void methods.


Name | Description
---------|----------
 `doReturn(Object)` | In case you cannnot use `when()`. e.g. spy objects
 `doThrow(Throwable)` | Used when the void method will throw an exception
 `doAnswer(Answer)` | Stubs a void method with a generic answer
 `doNothing()` | By default void methods do nothing but it can be uselful when stubbing consecutive calls
 `doCallRealMethod()` | When you want to call the real implementation of a method

## Argument Matchers

Mockito argument matchers give you more flexibility when you are writing stubs. A long list of matchers is provided this framework, and some of the most used ones are:


Name | Description
---------|----------
 `any()` | Matches anything, including nulls and varargs.
 `any(Class<T> type)` | Matches any object of given type, excluding nulls.
 `any<Type>()` | Any of the given "Type". e.g. `anyInt()`.
 `argThat(ArgumentMatcher<T> matcher)` | For custom argument matchers.
 `contains(String substring)` | Contains substring.
 `startsWith(String prefix)` | String argument that starts with the given prefix.
 `endsWith(String suffix)` | Ends with given suffix.
 `eq(T value)` | `T` argument that is equal to the given value.
 `isA(Class<T> type)` | Object argument that implements the given class.
 `isNotNull()` | Not null argument.
 `isNull()` | Null argument.
 `matches(Pattern pattern)` | For regular expressions.

{% include elements/highlight.html text="If you use an argument matcher in a method call, the rest of the argument have to be argument matchers." %}

## Verify Behaviour

Mockito does not automatically verify all stubbed calls because the tested code should focus on values returned by stubbed methods. However, sometimes you might want to verify if a particular mock  was called. You can use Mockito `verify(mock).<method>` to check if a particular method was called.

```java
verify(fooRepository).getNumbers();
```

By default `verify(mock)` checks if a given method was called once. But this can be customized by using verification modifiers.

List of verification modifiers:

 Name | Description
---------|----------
 `times(int)` | The method is called the n times
 `never()` | The method from the mock is never called
 `verifyNoInteractions(mock)` | Verifies no interaction with any method of the given mock
 `verifyNoMoreInteractions(mock)` | Checks if the given mock has any unverified interaction
 `atLeastOnce()` | The method called at least once
 `atLeast(int)` | The method called at least n times
 `atMost(int)` | The method called at motst n times
 `only()` | Checks if the given method was the only called

 The following example verifies if the method getNumber given any integer was called three times.

```java
verify(fooRepository, times(3)).getNumber(anyInt());
```

The following example verifies if the method get number given any integer was called once and there as no more interactions with the object `fooRepository`.

```java
verify(fooRepository).getNumber(anyInt());
verifyNoMoreInteractions(fooRepository);
```

### Verify Order

Sometimes you may want to verify the execution order of a mock.

```java
@Test
public void getNumberVerifyInOrderTest() {
    // GIVEN
    when(fooRepository.getNumber(anyInt())).thenReturn(8, 3, 5);

    // WHEN
    fooService.getNumber(1);
    fooService.getNumber(0);
    fooService.getNumber(2);

    // THEN
    InOrder inOrder = inOrder(fooRepository);
    inOrder.verify(fooRepository).getNumber(1);
    inOrder.verify(fooRepository).getNumber(0);
    inOrder.verify(fooRepository).getNumber(2);
}
```

You can create an `InOrder` instance and pass the mocks you want to verify in order.

## Using Spies

Spies are partial mocks. This means that when you use a spy then the real methods are called unless a method was stubbed. A spy is create with the annotation `@Spy` or by creating an instance of the class you want to spy and passing the instance to `Mockito.spy()`.

```java
@Spy
private FooRepositoryImpl spy;

@Test
public void saveAndGetFirstNumberWithSpyTest() {
    doReturn(4).when(spy).getNumber(anyInt());
        // throws IndexOutOfBoundsException because the real method is first called
//        when(spy.getNumber(0)).thenReturn(4);

    Integer firstNumber = fooService.saveAndGetFirstNumber(3);

    assertThat(firstNumber).isEqualTo(4);
    verify(spy).saveNumber(3);
}
```

In the previous example `getNumber` method is stubbed, so when `saveAndGetFirstNumber` is invoked  `getNumber` will return the value given in the stub and will call the real `saveNumber` method with the value `3`.

>Sometimes it's impossible or impractical to use `when(Object)` for stubbing spies because the real method will be called first and might throw an exeption. to prevents unnecessary calls to a real method during stubbing use `will` + `given` or `do` + `when`. e.g. `doReturn(4).when(spy).getNumber(anyInt());`

## Dependency Injection

`@InjectMocks` is used in combination with `@Mock` or `@Spy` to inject mocks or spy fields into the object under test automatically.

```java
@Mock // @Spy is also possible
private FooRepository fooRepository;

@InjectMocks
private FooServiceImpl fooService;
```

## Reset Mocks

In some cases it is required to reset a mock, for instance, you might use a container-injected mock like the ones from [Spring Framework](http://sergiomartinrubio.com/articles/spring-bean-lifecycle-fundamentals) to forget any interactions and stubbing of a particular mock.

```java
@Test
public void getNumbersWithResetTest() {
    when(fooRepository.getNumbers()).thenReturn(List.of(4, 6, 3));
    List<Integer> numbers = fooService.getNumbers();
    assertThat(numbers).contains(4, 6, 3);

    reset(fooRepository);

    List<Integer> emptyNumbers = fooService.getNumbers();
    assertThat(emptyNumbers).isEmpty();
}
```

>The use of reset is usually a code smell and it means you are testing too much in the same test method. Try to keep tests small and focused on a single behaviour.

## Capture Arguments

You can assert argument after verification with captors. This is an alternative to argument matchers during stubbing and in a similar way, it allows you to check if a mock was actually called with a particular value.

To use a captor you can simply declare an `ArgumentCaptor` with the class that you want to capture, and when you call `verify()` you will pass the argument captor object so you can write some assertions later.

```java
@Test
public void verifyWithCaptor() {
    fooService.saveAndGetFirstNumber(5);

    ArgumentCaptor<Integer> argument = ArgumentCaptor.forClass(Integer.class);
    verify(fooRepository).saveNumber(argument.capture());
    assertThat(argument.getValue()).isEqualTo(5);
}
```

## Behavior Driven Development

A test should be split into three blocks, each with a specified responsibility.

- **GIVEN**: Mock initialization, configuration and stubs.
- **WHEN**: Call to the _SUT_ (System Under Test) which can a method call.
- **THEN**: Assertions and verifications.

Mockito provides `given()` and `willReturn()` which are aliases for the `when()` and `thenReturn()` while stubbing, so it can be integrated with the Behavior Driven Development semantic. Similarly, `verify()` can also be replaced with `then()` and `should()`.

e.g. 

```java
@Test
public void getStringTest() {
    // GIVEN
    given(fooRepository.getFooMessage()).willReturn("Hello");

    // WHEN
    String message = fooService.getString();

    // THEN
    assertThat(message).isEqualTo("Hello");
    then(fooRepository).should().getFooMessage();
}
```

>GIVEN, WHEN and THEN comments make intentions of tests clearer. You might also consider a proper name your tests like `<testName>Given<configuration>When<somthingHappens>Then<expectations>`.