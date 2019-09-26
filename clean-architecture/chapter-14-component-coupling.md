# Chapter 14 - Component Coupling

These three principals deal with the relationship between components.

## Acyclic Dependencies Principle

> Allow no cycles in the compoent dependency graph

####The Problem
When many developers are modifying the same source files and keep stepping on each others code. Changes from different developers keep colliding and breaking each others code.

#### Solution 1
Everyone ignores each other's changes for 4/5 days (build period). On day 5 everyone comes together and tries to get everyone's code working together to produce a new build (integration period). Good for small-medium sized projects. As project grows, the integration period grows and the build period shrinks.


#### Solution 2
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

```
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


**1. Dependency Inveersion Principle**

Wrap one of the components in an interface within the component that needs it.

**2. Create A New Component**

Create a new component that two of the components can depend on.

Other Notes:

- Expect the dependency graph to change over time and watch out for cycles.
- Don't try and design a DAG before writing your code. DAG's are a way of viewing the _buildability_ and _maintainability_ of the app. It's a tool to help inform decisions about how to structure your codebase so it remains easy to _build_ and _maintain_.


## Stable Dependencies Principle (SDP)

> Depend in the direction of stability

By conforming to Common Closure Principle (CCP) some components are known to be volatile and are made easy to change. Inversly, other components may be difficult to change. By following SDP we ensure that components that are intented to be easy to change are not depended on by modules that are hard to change.

**Stable Component**

When a component (A) is used by by other components (X, Y, Z), it is considered stable. Think of component A as the base, and X, Y, and Z as the components build on top of that base, component A. Note that component A does not depend on anything else. 

Example: Lots of things depend on the `TCP` protocol, therefore `TCP` has many reasons to NOT change. It's foundational.

Another example: People really like stable API's, right?

**Unable Component**

When a component (Y) depends on other components (A, B, C), it is considered unstable. Component Y has three potential reasons to change. Note that nothing depends on component Y. 

Example: A website has many reasons to constantly be changing. Security best practices change (HTTP to HTTPS), website aesthetics change (glossy to matte buttons), you depend on Google SEO to drive traffic to your site so you have to make your site with AMP.

**How To Measure The Stability Of Components**

Just do math! `i` is for _instability_.

```
i = fanOut / (fanIn + fanOut)
i = 0 [maximally stable]
i = 1 [maximally unstable]
```
Where:

- `fanOut`: number of components that your component relies on (i.e. how many classes is my class calling?)
- `fanIn`: number of components that rely on your component (i.e. how many classes are calling my class?)


Given the above, we generally want to see this DAG.

```
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

```
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
          i=0      |
                   |
                   |
                 +-v----------+
                 |            |
                 |  Flexible  |
                 |            |
                 +------------+
```

If you do end up in this situation though, the common solution is Dependency Inversion. This will create an interface with an `i=0`.

```
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

TODO