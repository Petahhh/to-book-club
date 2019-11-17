# Chapter 5 - Maintaining the Test-Driven Cycle

* the theme of the chapter is to keep TDD alive

## Start Each Feature with an Acceptance Test

* start with writing a failing acceptance test case
* it's important to keep the acceptance test focused around the application's domain
  * e.g. do not "hard core" system level integrations as they may change (databases or web servers)
* the **failing** test helps us stay focused on delivering only as much as needed
* the exercise of writing a precise tests helps us better understand the needs of the user
* acceptance tests validate the integration of well united-tested classes/components and keep the project moving forward and delivering value

## Separate tests that measure progress from those that catch regressions

* acceptance tests describes new features we want to deliver and making them pass is part of the feedback loop that tells us we're moving forward
* unit tests and integration tests should **always** be run quickly and always pass - these are for the development process
* acceptance tests on the other hand can be organized in two parts
  * **regression suite** - (potentially slower) tests that prevent us from breaking features we've already delivered
  * **in-progress suite** - tests that represent the new features we're aiming to deliver
    * these tests should be moved to regression suite once they are delivered

```
+-------------------------------+ +-------------------------------+
|        Must be Green          | |        Starts off Red         |
+-------------------------------+ +-------------------------------+
+------------+ +----------------+ |                               |
|Must be fast| | Can be slow    | |                               |
+------------+ +----------------+ |      In-Progress Suite        |
| Unit       | |Regression Suite| |                               |
| Integration| |                | |                               |
+--------------+--------+-------+ +------------+------------------+
                        ^                      |
                        |                      |
                        +-+Moved when Green+---+
```

## Start with the Simplest Success Case

* it's tempting to start with the unhappy path but the author suggests **simple != simplistic**
* we should start with the **simplest success case** as it validates our idea, better for morale and feeds the feedback loop
* as we're working towards passing the failing success case, we can take note of possible refactors, edge cases and error handling for later. focus on passing the failing test first and go back for the rest before officially "delivering the story"

## Write the Test That You'd Want to Read

* start by focusing on the content of the tests (ignoring the fact you need it to compile) so it is a clear expression of what the intent of the system/class is
* then write supporting code so the test returns a clear error message **describing what needs to be done**
* no feature code should be written until the above is achieved

## Watch the Test Fail

* not only do we want to start with a failing test - it must **correctly** tell us it's failing
  * it's not good enough to see the test fail - it has to fail for the right reason
  * the failure **needs to clearly** help us know what to do. This is the **diagnostics** which must be **clear**
* good diagnostics help us easily and quickly maintain the regression test suite as things break over time 
  * thus we must continue to refactor the tests so they continue to give clear diagnostics 
  * good diagnostics also informs feature readers what the intentions of the tests are and the behavior of the code

```
+------------+    +-----------------+
|  Write a   |    |    Make the     |
|failing test+--->+diagnostics clear|
+-----+------+    +-------+---------+
      ^                   |
      |                   v
+-----+------+    +-------+---------+
|            +<---+    Make the     |
|  Refactor  |    |    test pass    |
+------------+    +-----------------+
```

## Develop from the Inputs to the Outputs

**Anti Pattern:** starting with unit testing domain objects
* this gives us the sense of rapid progress
* can be a waste of time as we can build things that either don't work or are not needed which we only find out once we integrate it

**Proper Flow**:

* start with Objects that receive an external event
* then intermediate layers
* then the central domain model
* finally the boundary objects that generates an external response

## Unit Test Behaviour Not Methods

* writing lots of tests to increase code coverage does not always lead to easily readable and maintainable code
* it's important to test the behavior/the **feature** the object under test should deliver

> "We need to know how to use the class to achieve a goal, not how to exercise all the paths through its code"

* choosing good behaviour-driven names is important to achieve this than just moving through the motions of increasing coverage via all the paths

## Listen to the Tests

```
+---------------+      +------------+
|Write a failing+----->+            |
|   unit test   |      |  Refactor  |
+--+------------+<-----+--------+---+
   ^                            |
   |     +-----------------+    |
   |     |    Make the     |    |
   +-----+    test pass    +<---+
         +-----------------+
```

> "Difficulties writing the tests may suggest a need to fix production code"

> **"The same structure that makes the code difficult to test will make it difficult to change"**

* the current moment is the best time to make this type of refactor because it is when we have the most context
  * considering the longevity of the code - we may not be the person to touch this area of the code next so it's better to get it down now
  
## Tuning the Cycle

* we need to continuously weigh the costs and benefits of either higher level tests or unit tests
* a key question to ask is: is the confidence justified?
* use more unit tests for more complex logic
* use more integration tests for system level error handling
