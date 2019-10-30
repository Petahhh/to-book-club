# Chapter 1 - What is the point of TDD?

## Software Development is as a Learning Process

* developers are learning about the components as they are building it
* it's impossible for a developer to understand all the possibilities of their program
* change is inevitable and the goal is to "anticipate unanticipated change"

## Feebdack is the Fundmental Tool

* empirical feedback tells us if our assumptions about the problem space are correct
* we need feedback cycles to improve our product
* there are many layers of feedback: unit tests for functionality of code or at a larger level like customer rentention for whether the product meets their needs
* the faster the feedback, the better

## Practices That Support Change

To reliably grow a system and cope with change we need:

1. Constantly catch regression errors so adding new features don't break existing ones
1. Keep code as simple as possible so it's easy to modify

* developers spend way more time reading code than writing code
* clean code makes it easy to move fast
* clean code requires time and energy for refactoring and tests help with that
* often developers don't find writing tests valuable work - instead they wish there were writing a feature
* TDD:
  * is a design activity
  * helps us clarify our understanding and *what* we want to build
  * writing tests first encourages more modular and easier to understand code
  
> "**If we write tests all the way through the development process, we can build up a safety net of automated regression tests that give us the confidence to make changes**"

## TDD In a Nutshell

```
+---------------+         +---------+
|write a failing+-------->+Make the |
|unit test      |         |test pass|
+-------+-------+         +---------+
        ^                 |
        |                 |
        |                 |
        |                 |
   +----+----+            |
   |refactor +<-----------+
   +---------+
```

* TDD tells us **if it works** and if has **good design**

|Writing Tests|Running Tests|
|---|---|
|* clarifies specs (design)|* catches errors when we have fresh context|
|* encourages **good** design|* helps stop over building (*"gold plating"*)|
|* adds a description of **what** the code does||
|* adds a complete regression suite||

### The Golden Rule of TDD

> "**Never write new functionality without a failing test**"

## The Bigger Picture

* unit tests are great but doesn't guarantee the set of methods will all come together and deliver an outcome
* **acceptance tests** exercises the functionality you want to build - it tells you that the feature is *not implemented* or when it *is implemented*
* acceptance tests is a sign of progress in the system while unit tests help developers get fast feedback and act as a regression safety net

This is the outer loop of TDD

```
+---------------+      +---------------+         +---------+
|write a failing|      |write a failing+-------->+Make the |
|acceptance test+----->+unit test      |         |test pass+----+
+---------------+      +-------+-------+         +---------+    |
       ^                       ^                 |              |
       |                       |                 |              |
       |                       |                 |              |
       |                       |                 |              |
       |                  +----+----+            |              |
       |                  |refactor +<-----------+              |
       |                  +---------+                           |
       |                                                        |
       |                                                        |
       +--------------------------------------------------------+
```

## Testing End-to-End

* acceptance tests ideally:
  * test the system from the outside without directly calling code from the inside
  * are fully automated from the time code is checked in
  * packages and deploys the code in a realistic environment
* passing acceptance tests means the code is shippable

## Levels of Testing

|Acceptance|Does the whole system work?|
|Integration|Does our code work against code we can't change?|
|Unit|Do our objects do the right thing? Are they convenient to work with?|

## External and Internal Quality

* External quality is customer facing - do we solve the problems they're paying to be solved?
* Internal quality is developer/management/admin facing - does the code allow us to easily understand it? easily make changes? minimize risks of major rework?
* While unit tests tell us about the quality of code and end to end tests tell us the external quality of the system, integration tests fall somewhere inbetween and tell us the quality of the system

