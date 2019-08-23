# Chapter 3 - Functions

* Large classes can be really hard to read (hard to read being takes a lot of time and energy to understanding). Breaking it up into smaller well named functions can make it a lot easier to understand.
* Functions should be **as small as possible.** There is no prescribed rule but the tighter the scope of the function the better. Aim to create method names that can encapsulate the single thing the function does and refactor/make the method smaller to help with this.
* If possible, the body of for/while blocks should be a single method call. This is helpful as self documenting code.
* In an ideal world, no method goes more than 2 layers deep with indentation - lots of helper methods can help with that.
> “Functions should do one thing. They should do it well. They should do it only”
* Functions that truly do one thing become really hard to break up.
* **One level of abstraction per function:** It can be confusing to read a function where there is more than one abstraction happening. It’s hard to think of an example for this one but it seems like if the function really does one thing it’s hard to introduce more than one abstraction layer.


## The Step Down Rule

* Function names and their lines can have the following format: 

>“To X, We need to A, B, then C”

```
Void CreateGatewayReceiver() {
  Config = createGatewayReceiverConfig();
  Cache = getCache();
  cache.saveGatewayReceiver(config)
}
```

The above can be read:

> To create a gateway receiver
>
> We first create a config object
>
> Then we get the cache
>
> Then we save the config with the cache"

* The idea is that every function is a step with sub steps and can be read like an English paragraph. If each helper function follows the same pattern, you can expand it all together to form an essay-like piece of text.

## Switch Statements

* Switch statements are inherently bad because they 
  * imply the function is or will be very large as the cases scale.
  * Break the rule of “do one thing only”
* It’s better to create an “Abstract Factory” and bury the switch statement within a simple method behind the factory so that people can avoid reading it.
Example: Say you have a set of class `Employee` that have different roles. A switch statement might be tempting for a method like `Employee.calculatePay()`. It’s tempting to put a switch statement in this method to handle the unique calculation rules for each role. Instead you can create an abstract class `Employee` such that each subclass of it can have it’s own `.calculatePay()`. To keep it easy to use you can create a factory to create different subclasses of `Employee`.

## Use Descriptive Names

* Don’t be afraid to use longer names - it’s better than requiring a comment
* Investing time into the right name is worth it: 
> [A sign of a great method name is when] the function is pretty much what you expected
* Invest time into restructuring your code if it helps make it easier to write easy to describe functions. Sometimes having a really difficult time coming up with a descriptive name is a signal the structure of the code can be simpler.

## Function Arguments

The number of Function Arguments should be kept to a minimum to maximize readability and minimize complexity

* No arguments are the best (**niladic** functions)
* One argument is okay (**monadic** functions)
Two arguments not as good (**dyadic** functions)
* Three arguments (**triadic** functions) should be avoided as much as possible
* **Polyadic** (more than 3 args) **should not be used** unless there is special justification.
You can keep arguments to a minimum by wrapping multiple variables in a construct or using class variables if it makes sense.
* Class variable vs instance variable
  * Instance variable: 
    * Example: class Car, it can have instance variables like colour
  * Class variables:
    * Static variable
    * One value for all instances
    * Example: class Mustang, it can have class variable such as “set of colours possible”

* **Flag arguments** (boolean inputs to distinguish two paths) can be replaced with two methods
  * `sendData(data, Boolean withConfirmation)`
  * vs 
  * `sendData(data)`
  * `sendDataWithConfirmation(data)`
* You can name functions with argument information to give the reader quicker understanding without reading up the declaration.
  * `AssertEquals(expected, actual)` vs
  * `AssertExpectedEqualsActual(expected, actual)`
  
*** 
* Function names with embedded information can be helpful (as long as it’s not encoding information).
  * `freeze(gatewaySender)` vs
  * `freezeProcess(gatewaySender)`
  * In this example gatewaySender is a process - but being extra explicit in the name and embedding that information makes it easier on the reader. They don’t have to open up gatewaySender’s class definition to learn it’s a process.
* **No side-effects:** If you’re following the “do one thing only rule” this is obvious. Functions should not have side effects that aren’t obvious in their name. This is where bugs can creep up.
* **Avoid output arguments** - In some older languages an output argument is required. Avoiding them in modern programming languages makes code easier to read.

> "Functions should do something or check something, not both” 
* `Boolean setProperty(someField);` //returns true if successful, false if property does not exist
* `if(setProperty(myfield)): X; else: Y;` // this requires the reader to dive into how setProperty works to understand the significance of the boolean return value vs
* `Boolean isProperty(someField)`
* `Void setProperty(someField)`
* It’s easy to avoid this when following the “do one thing only” rule. It’s sometimes tempting to provide extra information to the user when operating on something but it’s better to break out into separate functions.

## Exceptions > Error codes

* Error codes are not as preferable because they:
  * take a lot of energy to maintain and do mental mapping of their meanings 
  * May require a lot of if/else blocks to manage the outcomes of operations
* Try catch blocks are a bit easier to read then if/else statements that check error codes

***

Pull out try and catch blocks into their own methods. It’s easier to read “try X catch Y” at a high level than read the implementation of X and Y all in one go. Also these helper methods you replace your try catch blocks with can describe the intention of the block making it easier to read. Also “handling an error is **one thing**” so they deserve their own method too.


## DRY: Don’t Repeat Yourself

* If two methods have a lot of overlap - refactor them
* “Favour duplication in lieu of using the wrong abstraction”

***
* Structured Programming: A concept that every function should have only one entry point and one exit point (only one return statement). While an interesting and good aspiration, as a hard and fast rule not practical to follow.
* Writing functions is like writing an essay - first drafts help you get your thoughts on paper, second and third revisions to iron out the kinks make it readable for others.

