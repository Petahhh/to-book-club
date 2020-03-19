```
+-------------------------+        +----+
| Source Method           |        |
|                         |        |
|                         |        |
|  +--------------------+ | <------+   Sprout Method/Class
|                         |        |
|                         |        |
|                         |        |
+-------------------------+        +----+

```

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

### When not to use sprout method

When the following is true:
* You need to make changes to a class
* Creating objects of that class within a test harness is hard
  * hard = the balance between how much time you're able to spend vs how much effort it is
* Creating an instance of the class has a long chain of dependencies

```
B = new B("hello", "world")
C = new C(B, 1, 2, 3)
.
.
.
X = new X(B, C, W, T, F)
```

## Sprout Class

* When dependencies are really tangled, the sprout class may be better than a sprout method
  * Example of when sprout method
* Sprout classes can be used in lieu of the sprout method. Sprout classes hold your changes and is used from the source class.
* It may be unintuitive to create small classes for something that feels insignificant however when you do it enough better patterns may emerge like an interface. Ideally your untested large class evolves (along with it's testing suite) to look more like the interface or several small classes you add.
* Sprouting small classes isn't a just a quick fix, it's part of many iterations that slowly improve the design of your code
  * Sprout classes may lead you to better understand the responsibilities of the class and thus help you justify the new class and it's new responsibility
Peter: new directory with one file in it problem
  * sometimes my gut tells me to pull something out into it's own class
  * the new class doesn't really fit with anything else
  * I end up putting it into it's own directory all by it's lonesome self - feels icky

### Sprout Class Step by Step

1. Find **where** you need to make a change (source method)
1. If the change you need to make can be made by adding a set of sequential lines, thank of a **good class name**. Comment in the instantiation of a sprout class and a method call to use it
1. **Inputs**: What local varialbles do you need from the source method? Add them as arguments to sprout class' constructor method
1. **Outputs**: What does the class need to return to the source method? If any, create a method in the sprout class to return it. Update your commented code in the source method to call the method and receive the output.
1. Test drive the sprout class' method
1. Comment in the code you added to the source method.

|Pros|Cons|
|---|---|
|Gives you confidence in making changes that may be invasive|Adding complexity to the code base|
|Minimal changes to existing code which minimizes risk|You may be adding responsibility to the sprout class that may truly belong in the source method/class|
|Creating a precendence/pattern that make similar future changes easier||
|You're not making a bad situation worse||

## Wrap Method

**Temporal Coupling**:

* When you add functionality to a piece of code because the new functionality needs to happen at the same time as the code. It's convenient and it'll get the job done. The risk is that this is easily abused.
* This becomes problematic when:
  * You may eventually need these two things to happened separately
  * These two things have grown together with no clear seam and becomes hard to separate
  
```
 Flow diagram:

+---+     +----------+      +-----------+
|   |     |          |      |           |
|   +---->+ Event A  +----->+  Event B  |
|   |     |          |      |           |
+---+     +----------+      +-----------+
          |          |
          | New Event|
          |    A.1!  |
          +----------+
``` 

In lie of this icky code you can create a wrap method. It looks like this:

```
 Flow diagram:

+---+     +-------------------------+      +-----------+
|   |     | Wrap Method             |      |           |
|   +---->+   +                     +----->+  Event B  |
|   |     |   |                     |      |           |
+---+     |   |     +----------+    |      +-----------+
          |   |     |          |    |
          |   +---->+ Event A  |    |
          |   |     |          |    |
          |   |     +----------+    |
          |   |                     |
          |   |     +----------+    |
          |   |     |   New    |    |
          |   +----->Unrelated |    |
          |         |   EVent  |    |
          |         +----------+    |
          +-------------------------+

```

1. Create a wrap method that has the same signature of the method you're tempted to do temporal coupling to
1. Have the wrap method call the original method
1. Add a new method for your new functionality
1. Have the wrap method also call your new method

This a technique is also called the **decorator pattern**

### Decorator Pattern

We know that change is inevitable. In situations where you have a set of behaviours that might be tweaked and changed in the future you can use the decorate pattern to make it easier to adapt to changes in the future.

Consider a smart remote that controls the lights, blinds and TVs in your home:

```
interface SmartRemote {
  TurnOff(string device)
  TurnOn(string device)
}
```

You can easily implement this object but what would it look like to create a decorator version of it? Start with an abstract class

```
abstract class SmartRemoteDecorator implements SmartRemote
```

Give it a constructor with an instance of SmartRemote as an parameter

```
abstract class SmartRemoteDecorator implements SmartRemote {
  SmartRemoteDecorator(SmartRemote init)
}
```

"Cheat" by reusing the instance of SmartRemote for all the methods of the interface


```
abstract class SmartRemoteDecorator implements SmartRemote {
  SmartRemoteDecorator(SmartRemote init)
  
  this.TurnOn() {
    init.TurnOn()
  }
  
  this.TurnOff() {
    init.TurnOff()
  }
}
```

The above is an example of the decorator pattern that wraps an existing class. Anyone who uses it doens't need to know it's a wrapper. The convenient thing about this is you've setup the infrastructure to extend the behaviour of TurnOn without touching the implementation of SmartRemote. For example you could add logging to datadog so people can creep you.


```
abstract class SmartRemoteDecorator implements SmartRemote {
  SmartRemoteDecorator(SmartRemote init)
  
  this.TurnOn() {
    init.TurnOn()
    dataDogApi.Log("Peter is probably home")
  }
  
  this.TurnOff() {
    init.TurnOff()
  }
}
```
