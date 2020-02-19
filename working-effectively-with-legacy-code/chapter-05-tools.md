# Tools

**tl;dr** this is a short chapter with little exposition. It contains stories,
name-dropping, Smalltalk, Java, C++ and .NET. I will try to call out the parts
that seem interesting.

## Automated Refactoring Tools

**refactoring**: A change made to the internal structure of software to make it
easier to understand and cheaper to modify _without changing its existing
behaviour_.

IDEs often have the ability to automatically perform certain refactorings -
however many of them do not guarantee that they preserve behaviour. Often they
simply make context-insensitive changes at the syntax level, which can introduce
bugs. To the extent possible, it is worth introducing tests even before
performing automated refactorings.

e.g.

```java
public class A {
  private int hasSideEffect() {
    return new Random().nextInt();
  }
  private void takesInt(int arg) {
  }
  public void gonnaRefactor() {
    // let's get rid of the variable v, right!?
    int v = hasSideEffect();
    for (int i = 0; i < 10; i++) {
      takesInt(v);
    }
  }
  
}
```

can, in many tools, be refactored to

```java
public class A {
  private int hasSideEffect() {
    return new Random().nextInt();
  }
  private void takesInt(int arg) {
  }
  public void gonnaRefactor() {
    // oh no, hasSideEffect is now getting called 10 times!
    for (int i = 0; i < 10; i++) {
      takesInt(hasSideEffect());
    }
  }
  
}
```

## Mock Objects

Yep.

## Unit-Testing Harnesses

Not all testing tools are FLOSS, but... the good ones are?

xUnit - key features that we mostly take for granted:
* Programmers write tests in the language they are developing in.
* All tests run in isolation
* Tests can be grouped into suites so that they can be run and rerun on demand.

xUnit platforms:
* Java - JUnit
* C++ - CppUnitLite (created by the author)
* .NET (C#, VB.NET, etc) - NUnit

## General Test Harnesses

### Framework for Integrated Tests (FIT)

HTML tables are input, HTML output, and programmers can write table-handling
hooks for executing them.

### Fitnesse (from Clean Code)

FIT in a wiki.
