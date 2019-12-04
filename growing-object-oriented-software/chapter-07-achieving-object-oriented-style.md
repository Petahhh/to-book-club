# Achieving Object Oriented Style

## How Writing a Test First Helps the Design 

1. Starting with a test means that we have to describe what we want to achieve before we consider how.
    This act of DEFINING what you're planning to create focuses our work.
2. To keep unit tests understandable, we have to limit their scope.
    Unit tests are only useful/maintainable when they're limited in scope, similarly to components/functions/methods. By defining the test first, we can more easily recognize scope creep before we get into the weeds coding.
3. To construct an object for a unit test, we have to pass its dependencies to it, which means that we have to know what they are.
    Trying to write tests for something that requires too many dependencies (and thus should be refactored) is really difficult, giving us a clear signal for when we should refactor *before* we get caught up in adding even more to our codebase

## Communication over Classification

We view a running system as a web of communicating objects, so we focus our design effort on how the objects collaborate to deliver the functionality we need.

In languages such as Java, we can use interfaces to define the available messages between objects, but we also need to define their patterns of communication, their communication protocols.

> An interface describes whether two components will fit together, while a protocol describes whether they will work together.

Using TDD with mock objects offers a chance to make these communication protocols visible in two contexts:
1. During development
2. As a description when revisiting the code

It also helps us with information hiding. The TDD process of creating mocks for an object's peers (dependencies, notifications, and adjustments) but not its internals, helps us identify whether or not a given object is a peer, or whether it should instead be internal to the target object.

## Values Types

Values are immutable, so they’re simpler and have no meaningful identity

Objects have state, so they have identity and relationships with each other.

There are three basic techniques values types: `Breaking out`, `Budding off` and `Bundling up`.

### Interesting talk 

Value Objects in Domain Driven Design by Dan Bergh Johnsson: https://vimeo.com/13549100

Summary: 

* We have lots of computational complexity in our systems that can be moved to value objects (especially compound value objects)
* This can reduce concurrency issues, make things easier to test, and make our code easier to reason about

## Breaking out, Budding off and Bundling up

* Breaking out: When we find that the code in an object is becoming complex.
* Budding off: When we want to mark a new domain concept in code.
* Bundling up: When we notice that a group of values are always used together, we take that as a suggestion that there’s missing construct.

### Breaking Out: Splittings a Large Object into a Group of Collaborating Objects

When starting a new area of code, we might temporarily suspend our design judgment and just write code without attempting to impose much structure.

Break up an object if it becomes too large to test easily, or if its test failures become difficult to interpret. Then unit-test the new parts separately.

### Budding Off: Defining a New Service that an Object Needs and Adding a New Object to Provide It

When the code is more stable and has some degree of structure, we often discover new types by “pulling” them into existence.

When writing a test, we ask ourselves, “If this worked, who would know?” If the right answer to that question is not in the target object, it’s probably time to introduce a new collaborator.

> Some people have a really hard time drawing a line between when budding off is useful and when it just adds pointless code. I think this sort of practice takes place naturally if you follow the Jeff Bay [Object Calisthenics](https://williamdurand.fr/2013/06/03/object-calisthenics/) rules.

### Bundling Up: Hiding Related Objects into a Containing Object

This is the application of the “composite simpler than the sum of its parts” rule.

When we have a cluster of related objects that work together, we can package them up in a containing object.

When the test for an object becomes too complicated to set up, when there are too many moving parts to get the code into the relevant state, consider bundling up some of the collaborating objects.

## Identify Relationships with Interfaces

We use interfaces to name the roles that objects can play and to describe the messages they’ll accept.

The fewer methods there are on an interface, the more obvious is its role in the calling object.

## Compose Objects to Describe System Behavior

TDD at the unit level guides us to decompose our system into value types and loosely coupled computational objects.

The tests give us a good understanding of how each object behaves and how it can be combined with others.

## Building Up to Higher-Level Programming

Organize the code into two layers: an implementation layer which is the graph of objects and, a declarative layer which builds up the objects in the implementation layer, using small “sugar” methods and syntax to describe the purpose of each fragment.

The declarative layer describes what the code will do, while the implementation layer describes how the code does it.

## Building Up to Higher-Level Programming

GOOS describes a manner of thinking about code in two layers to make things easier to reason about:

* an 'implementation' layer which is a graph of objects, and whose behavior is the combined result of how its objects respond to events, and
* a 'declarative' layer that builds up the objects in the implementation layer

For the implementation layer we stick to the conventional object oriented style guidelines.

For the declarative layer we might even use “train wreck” chaining of method calls or static methods to help get the point across.

Our purpose, in the end, is to achieve more with less code.

We aspire to raise ourselves from programming in terms of control flow and data manipulation, to composing programs from smaller programs, where objects form the smallest unit of behavior.

## And What about Classes?

We view classes for objects as an “implementation detail”, a way of implementing types, not the types themselves.

## Supplemental reading

Pixie Driven Development by Liz Keogh
https://lizkeogh.com/2009/07/01/pixie-driven-development/

This blog post details a mental exercise that makes it easier to compartmentalize functionality and decide how big to make different objects and classes. The author imagines a team of very eager but useless pixies that volunteer to handle each function as soon as the author has imagined it, but are too lazy to take on more than one responsibility. They'll delegate more complicated work to a different pixie (or some other system or bit of code), but don't want to do anything more than the one responsibility they've volunteered for.

The results of the mental exercise is that "you’ve got simple messages going back and forth. Your code is very tolerant of extra information, as is the code on this side. You’ve got lovely RESTful URLs, because you were thinking about how you’d like to find us, instead of us providing you with some weird mechanism that doesn’t match exactly what you wanted. We’ve got clean interfaces and APIs. There are no extra columns in your database, because you only replaced exactly what we said we’d do in the first place. You’ve got scenarios to describe how we work together, and at a unit level examples to describe how you’re delegating responsibility to the other pixies. It’s a lovely, maintainable system with a fairly flat cost of change."
