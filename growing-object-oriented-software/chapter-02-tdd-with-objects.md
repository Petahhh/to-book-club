# Chapter 2 - Test-Driven Development with Objects

## A Web of Objects

* OO Design is more focused on the communication between objects than the internal properties and behaviour of the objects
* objects communicate to each other through *messages*
    * i.e. return values

### Values vs Objects

*Values*: models fixed quantities or measurements. Values are functional.
    * have no individual identity (i.e. two integer variables with a value of `2` are effectively the same)
*Objects*: models computational processes and have an identity that can change over time. Two objects may currently have the same state but they may diverge later if they recieve different messages.

*Communication patterns*: can be enforced in languages (example: Java uses interfaces, abstract classes to act as a set of rules for which objects can send messages to each other

Authors want us to focus on the communcation patterns between objects when thinking about how to design a system.

You can think about systems as *static objects* or *dynamic communcation structures*. Author encourages the latter because the communication patterns between objects best represent the domain model of the system.

Example of shifting to this type of thinking: video games!

If you think about the objects in a video game you typically have: actors (player, NPC's, enemies), scenery (static images the player walks by), obstacles (things actors can crash into), and effects (explosions, smoke, etc.).

Thinking about the game objects works well if you're the player but not if you're the one implementing the game.

The game implementers care about: displaying objects that are visible to the player, tell animated objects about the passage of time, detecting collision between objects and deciding how to resolve the collision. These things are all focussed around the *communication* between objects in the system.

### Tell, Don't Ask / Law of Demeter

When one object calls another it should describe what it wants in terms of the role that the other object plays.

Objects should make decisions based on the information they hold internally or the information that came with the triggering message. Following this style produces more flexible code.

When you don't follow this style, you get this:

```
((EditSaveCustomizer) master.getModelisable()
  .getDockablePanel()
      .getCustomizer()
            .getSaveItem().setEnabled(Boolean.FALSE.booleanValue());
```

When you follow *"Tell, Don't Ask"* you'll get something like this instead:

```
master.allowSavingOfCustomisations();
```

A subtle benefit of this style is that it forces us to explicitly name the interactions between objects, instead of leaving the interaction implicit in the chain of getters from the first example.
The shorter version explains *what* needs to happen, while the longer first example is focussed on *how* the change is implemented.

**But Sometimes Ask**: we don't *tell* everything. Sometimes we *ask* when getting info from values, collections, or using a factory to create new objects.

### Unit Testing Collaborating Objects

Following the Law of Demeter we end up with objects that are harder to write `asserts` for in our unit tests because we can't query their internal state. How do we TDD the communcation between our objects? **Mock** your objects that aren't the target of your unit tests.

Essential structure of a test:

* create any required mock objects
* create any real objects, including the target object of the test
* specify how you *expect* the mock objects to be called by the target object
* Call the triggering method(s) on the target object
* assert that any resulting values are valid and that all the expected calls have been made
