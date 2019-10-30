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

1. Write a failing unit test
1. Make the test pass
1. Refactor

* TDD tells us **if it works** and if has **good design**

|Writing Tests|Running Tests|
|---|---|
|* clarifies specs (design)|* catches errors when we have fresh context|
|* encourages **good** design|* helps stop over building (*"gold plating"*)|
|* adds a description of **what** the code does||
|* adds a complete regression suite||

### The Golden Rule of TDD

> "**Never write new functionality without a failing test**"
