# I Don't Have Much Time and I Have to Change It

* Is it worth spending 2 hours backfilling tests for 15 minutes of "real work"? (Real work being the specific change you need to make.) Yes because:
  * It would have taken more than 15 minutes to do the "real work" without the tests
  * It takes more time to debug mistakes
  * It takes more time to find errors
* When do you earn back your investment in clean code (well tested code, small functions/classes)?
  * Worst case scenario is years go by before anyone looks at it again
  * Commonly changes within a code base happen in clusters within a system so you'll reap the rewards sooner than you'd think
* The first thing teams should start doing is slow down development to ensure everything they change has test coverage
  * It'll be slow and hard at first but once that initial hump is passed things get easier and people prefer it
  * When one does not know how to test something, there needs to be a meeting to help solve it
* Giving up tests to quickly deliver the feature is tempting but a bad idea
  * It is very hard to "go back after" to fill in tests and refactor
* When you're in a pinch to make changes to code quickly, try instantiating the class in a test harness
* You can add new code without backfilling and refactoring old code. This results in adding well tested new code. Do this cautiously as you'll still need to add test coverage on the old code calling the new code. It's not valuable to add the new well tested code if the coverage between isn't there.

## Sprout Method

* When you can, write a new feature to a system in new code.
  * It may be hard to create coverage in places where the old code calls the new cold but at least what you add is well tested
  
### The Sprout Method Step by Step

1. Identify where you need to make your code change
1. If the change can be made by adding a set of sequential lines, add in a comment in it's place with a method call. (This allows you to see how well the naming of the method is while in the context of it's usage). This is called the **sprouted method**
1. What variables do you need from the source code? Use them as parameters to your new method
1. Does your method need to return something? If so update the sprouted method to return a value
1. Use TDD to write the sprout method
1. Comment **in** the sprout method

* sprout methods are preferrable when you don't have time to test/have a hard time testing the source method
* testing the sprout method may require mocking out a lot of inputs, in which case the "Pass Null" technique can be used
  * If pass null is not possible, make the method static. It's weird but it makes testing easier.
  * Over time, when you have enough static methods you can fold them into it's own class. When the source class is fully tested, you can fold these back in.


### Pros and Cons

|Pros|Cons|
|---|---|
|New code and old code are clearly separated|You're giving up on the source class/method. Sometimes this is okay.|
|When unable to get old code under test, you see a clean interface between new and old code|Source class/method can be left in an odd state|
|You can see all variables affected in your change which helps determine if the code is correct||

## Sprout Class


