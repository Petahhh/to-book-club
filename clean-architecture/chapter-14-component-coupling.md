# Chapter 14 - Component Coupling

These three principals deal with the relationship between components.

## Acyclic Dependencies Principle

> Allow no cycles in the compoent dependency graph

### The Problem

When many developers are modifying the same source files and keep stepping on each others code. Changes from different developers keep colliding and breaking each others code.

### Solution 1 - Weekly Build

Everyone ignores each other's changes for 4/5 days (build period). On day 5 everyone comes together and tries to get everyone's code working together to produce a new build (integration period). Good for small-medium sized projects. As project grows, the integration period grows and the build period shrinks.

### Solution 2 - Remove dependency cycles

Break the codebase down into individual components that are each owned by one team. 

Golang example

- Put everything into your `main` package
- Break everything up into logical packages

PHP example

- Put everything in `main.php` (the original Single-page application!)
- Break-up everything into separate files and classes

By breaking the codebase into components, each component can decide if it wants to integrate the new version of the components it depends on. Each team can iterate for as long as they want and pay the integration price when it's appropriate for them.

This only works if the dependency structure is managed properly. _There must be no cycles_. Once there are cycles, deadlocking can occur between specific versions of the components that are part of the cycle. Every component caught in the cycle act as if they're one component again.

This diagram has no cycles. You can never loop back to the component that you started at. This is a _Directed Acyclic Graph_ (DAG).

```console
                     +-----------------+
                     |                 |
   +-----------+-----+     Main        +-------------+--------------+
   |           |     |                 |             |              |
   |           |     +--------------+--+             |              |
   |           |                    |                |              |
   |   +-------v---------+          |       +--------v--------+     |
   |   |                 |          |       |                 |     |
   |   |     Views       |          |       |   Controllers   |     |
   |   |                 |          |       |                 |     |
   |   +-----+-----------+          |       +-----------------+     |
   |         |                      |                               |
   |         |                      |                               |
   |         |                      |                               |
+--v---------v----+            +----v------------+        +---------v-------+
|                 |            |                 |        |                 |
|   Presenters    |            |   Interactors   <--------+   Authorizer    |
|                 |            |                 |        |                 |
+-----------------+            +-----------------+        +-----------------+
```

If a cycle is introduced there are two ways of breaking the cycle.

### 1. Dependency Inveersion Principle

Wrap one of the components in an interface within the component that needs it.

### 2. Create A New Component

Create a new component that two of the components can depend on.

Other Notes:

- Expect the dependency graph to change over time and watch out for cycles.
- Don't try and design a DAG before writing your code. DAG's are a way of viewing the _buildability_ and _maintainability_ of the app. It's a tool to help inform decisions about how to structure your codebase so it remains easy to _build_ and _maintain_.


## Stable Dependencies Principle (SDP)

> Depend in the direction of stability

By conforming to Common Closure Principle (CCP) some components are known to be volatile and are made easy to change. Inversly, other components may be difficult to change. By following SDP we ensure that components that are intented to be easy to change are not depended on by modules that are hard to change.

### Stable Component

When a component (A) is used by by other components (X, Y, Z), it is considered stable. Think of component A as the base, and X, Y, and Z as the components build on top of that base, component A. Note that component A does not depend on anything else. 

Example: Lots of things depend on the `TCP` protocol, therefore `TCP` has many reasons to NOT change. It's foundational.

Another example: People really like stable API's, right?

### Unstable Component

When a component (Y) depends on other components (A, B, C), it is considered unstable. Component Y has three potential reasons to change. Note that nothing depends on component Y. 

Example: A website has many reasons to constantly be changing. Security best practices change (HTTP to HTTPS), website aesthetics change (glossy to matte buttons), you depend on Google SEO to drive traffic to your site so you have to make your site with AMP.

### How To Measure The Stability Of Components

Just do math! `i` is for _instability_.

```python
i = fanOut / (fanIn + fanOut)
i = 0 [maximally stable]
i = 1 [maximally unstable]
```

Where:

- `fanOut`: number of components that your component relies on (i.e. how many classes is my class calling?)
- `fanIn`: number of components that rely on your component (i.e. how many classes are calling my class?)

Given the above, we generally want to see this DAG.

```console
+------------+      +------------+
|            |      |            |
|  Instable  |      |  Instable  |
|            |      |            |
+----------+-+   +--+------------+
i=1        |     |     i=1
           |     |
           |     |
         +-v-----v----+
         |            |
         |   Stable   |
         |            |
         +------------+
          i=0
```

This DAG is a violation of SDP. This _increases_ `i` and SDP says we should always lean towards stability, not instability. The below DAG would increase the `i` for `Stable` from `0` to `1/3`.

```console
+------------+      +------------+
|            |      |            |
|  Instable  |      |  Instable  |
|            |      |            |
+----------+-+   +--+------------+
i=1        |     |     i=1
           |     |
           |     |
         +-v-----v----+
         |            |
         |   Stable   |
         |            |
         +---------+--+
          i=1/3    |
                   |
                   |
                 +-v----------+
                 |            |
                 |  Flexible  |
                 |            |
                 +------------+
                 i=1 (for reasons...)
```

If you do end up in this situation though, the common solution is Dependency Inversion. This will create an interface with an `i=0`.

```console
+------------+      +------------+
|            |      |            |
|  Instable  |      |  Instable  |
|            |      |            |
+----------+-+   +--+------------+
i=1        |     |     i=1
           |     |
           |     |
         +-v-----v----+
         |            |
         |   Stable   |
         |            |
         +---------+--+
          i=0.3    |
                   |
                   |
                 +-v-----v----+      +------------+
                 |            |      |            |
                 |  Interface <------+  Flexible  |
                 |            |      |            |
                 +------------+      +------------+
                 i=0                 i=1
```

## Stable Abstractions Principle

> A component should be as abstract as it is stable

There's the idea that some software in a system should not change very often (database?). This software usually represent **high-level architecture** or **policy decisions**. Therefore this type of software should be placed in a stable component (_i = 0_).

Given these facts we now face a **problem**. How can a component that is maximally stable (_i = 0_) be flexible enough to withstand change?

The answer to this problem is the **Open-Closed Principle (OCP)** (open to extension, closed to modification). What kind of classes satisfy the OCP? **Abstract classes**.

What's the **Stable Abstraction Principle (SAP)** about then??

The SAP is about the relationship between the stability and abstractness of a component.

Therefore, stable components should be should be abstract, so it's not prevented from being extended. Stable components should consist of interfaces and abstract classes.

The SAP and SDP combined are the DIP of components. DIP is for classes.

What's the difference between components and classes? Components are a group of related classes working together to serve some function.

Why don't we just say the DIP is for classes and components? We don't say DIP is for components because components will always be a mix of different kinds of classes, they fall in a gray area. Classes are never gray; they're always abstract or not.

### Measuring Abstractness

```python
A is between 0 and 1
c = # of classes in a component
a = # of abstract classes and interfaces in a component
A = a / c
```

### Graphing Abstractness

Martin draws a graph with **Abstractness as the Y-axis** and **Instability as the X-axis**

```console
0,1|\                 1,1          x: Zone of Pain
   |  \                            y: Zone of Uselessness
   |    \           y              \: The Main Sequence
   |      \
   |        \
   |          \
   |            \
   |              \
   |  x             \
   |__________________\
0,0                    1,0
```

This graph can help us think about the relationship between abstractness and instability of components.

### The Zone of Uselessness

We should ensure our components don't fall into the **Zone of uselessness** (1,1). Components in this area are full of abstract classes/interfaces that aren't used at all - they literally have no dependents.

## The Zone of Pain (0,0)

When code is volatile *and* stable, it’s difficult to change because of downstream dependencies.

- One example would be a database schema, often highly volatile, concrete, AND depended on (which is basically what defines stability). Remember any time you've had to do database schema migration work.
- Greater volatility + Concrete classes === Greater Pain

## The Main Sequence

Overall you want components near the **Main Sequence** (the slope between (1,0) and (0,1)). 

What this means more specifically is that components will be stable and abstract, or volatile and concrete.

> Another way of saying this, is that we want instability XOR abtract

The line between these two conditions is called the “Main Sequence” – and it’s basically the “good” line.

### The Distance from the Main Sequence

We can figure out a measure for how good our code is by judging the distance from the main sequence:

```console
(D)istance = | (A)bstractness + (I)nstability – 1 |
```

This should produce a value between 0 and 1.

Some notes:

- It’s like golf, the lower the better
- We can look at the standard deviation to compare the quality of components – what are the outliers?
- Setting a threshold for identifying components to flag for evaluation is a way of keeping things in line
- If you're really keen you can plot these component changes over time to identify trends and pivotal commits/PRs
- From this point you can do more analysis on the data, like plot the mean and variance and even track how components drift on this graph over time
