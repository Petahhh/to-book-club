# Chapter 6 - Object-Oriented Style

> Always design a thing by considering it in its next larger context - a chair in a room, a room in a house, a house in an environment, an environment in a city plan.
>
> - Eliel Saarinen

This chapter focuses on the attributes of the object oriented design we want to achieve. In the next chapter, we'll talk about how to achieve this design using TDD.

### Design for Maintainability

As code scales up, the only way we can continue to maintain an understanding of the system is by structuring the functionality into objects, obects into packages, packages into programs, and programs into systems.

Heuristics to guide this structuring:

- **Separation of concerns:** Gather togther code that will change for the same reason. When we need to change the behaviour of a system we want to change as little code as possible. If relevant changes are in the same area of the codebase it makes implementing the change easier.
- **Higher levels of abstraction:** Humans deal with complexity by avoiding complexity; humans work with higher levels of abstraction. When programming, combine useful components of functionality rather than manipulate variables and control flow.

Applying the previous two principles will usually result in the "Ports and Adapters" architecture. Ports are interfaces and Adapters are implementations of Ports.

The next question we need to answer is: how to divide up the code cleanly? Where do our interfaces need to go?

Before proceeding let's level-set some words:

- Encapsulation: ensure the behaviour of an object can only be affected through its API and that there are no ripple effects between dependencies and unrelated components.
- Information Hiding: conceal how an object implements its functionality behind the abstraction of its API. When this is not followed you end up with "train wreck" code (`object.getPanel().getCustomizer().getSide().setWidth(value)` vs `object.setPanelWidth(value)`)

### Internals vs Peers

Objects communicate by sending and receiving messages through API's they provide to each other. Choosing how to communicate is very important, it determines how easy it is to use an object. If your API exposes too much of what you do (lack of information hiding) then other objects that use your object will probably end up implementing some of the work that your object does already. This makes it harder for your object to change and if it does change it may have ripple effects on its dependencies.

This can be shown via the `train wreck` example from the previous a previous chapter:

```
((EditSaveCustomizer) master.getModelisable()
	.getDockablePanel()
		.getCustomizer())
			.getSaveItem().setEnabled(Boolean.FALSE.booleanValue());
```

Each function in the chain sets a strong expectation on the expected behaviour of that function. This means that the object that exposed these functions (its api) will now struggle to change because its dependencies rely on this version of the API.

### Side-note: Different levels of language

We often write helper methods to make code more readable. We use a *message-passing* style when writing helper methods to communicate **between** objects. We use a more *functional style* when writing helpers for **within** an object.

Here's an example from the Concourse k8s-topgun test suite. This is using the Ginkgo testing framework. This is an example of a *functional style* helper method.

```go
var _ = Describe("baggageclaim drivers", func() {

	AfterEach(func() {
		cleanup(releaseName, namespace)
	})

	onPks(func() {
		baggageclaimWorks("btrfs")
		baggageclaimWorks("overlay")
		baggageclaimWorks("naive")
	})

	onGke(func() {

		const (
			COS    = "--set=worker.nodeSelector.nodeImage=cos"
			UBUNTU = "--set=worker.nodeSelector.nodeImage=ubuntu"
		)

		Context("cos image", func() {
			baggageclaimFails("btrfs", COS)
			baggageclaimWorks("overlay", COS)
			baggageclaimWorks("naive", COS)
		})

		Context("ubuntu image", func() {
			baggageclaimWorks("btrfs", UBUNTU)
			baggageclaimWorks("overlay", UBUNTU)
			baggageclaimWorks("naive", UBUNTU)
		})
  })
```

### Back to the main road - No And's, Or's, or But's

How do we choose the right features for an object? Another way: what should the focus of my object's API be?

Answer: SRP, Single Responsibility Principle

We should be able to describe an object with using any conjunctions (and, or).

This rule still applies when we're packing a collection of objects together. "The composite is simpler than the sum of its parts".

### Object Peer Stereotypes

We defined API's for how our objects will communicate. What do the objects say to each other though?

An object can have three types of relationships with its peers. These are heuristics, not hard rules!

#### Dependencies

Services that an object cannot function without. e.g. a graphics package needs a screen/canvas to draw on

#### Notifications

Some peers want to be kept up to date with an objects activity. For the object, this is usually a "fire and forget" sort of deal. The object doesn't care or know which peers are listening. Common in user interface systems. e.g. when a button is clicked it sends off a notification to any listeners that are subscribed to it. This decouples both objects from each other in a very clean way.

### Adjustments

Peers that adjust an objects behaviour to meet the wider needs of the system. e.g. In Concourse, there's an accessor object that can change the behaviour of the system based on what role a user has. Each API endpoint doesn't need to verify if a user has a specific role, the accessor is able to adjust the system to ensure each API endpoint is properly protected.

### Composite Simpler Than The Sum Of Its Parts

All objects in a system, expect primitive types built into the language, are composed of other objects. When combining objects to make a new type our goal should be to exhibit simpler behaviour in the one new object compared to all the individual objects that make it up.

For example, let's say we have the classic `money` object. It stores two pieces of information: the `currency` and the `amount`. We also have an `account` object that stores the same information.

```
account.SetAmount(money.Amount())
account.SetCurrency(money.Currency())
```

As the `account` object, I can make my API simpler but just taking in a money object. This exposes less of the internal workings of the `account` object.

```
account.SetValue(money)
```

This brings us to the golden rule of this section:

> The API of a composite object should not be more complicated than that of any of its components.

Following this rule helps us decide if an object is hiding enough information.

### Context Independence

The Context Independence rule helps us decide if an object hides too much or the wrong information.

A system is easier to change if its objects are *context-independent*. This allows us to take units of behaviour (objects) and apply them in new situations. To remain context-independent an object to have passed in whatever it needs to know about the external environment. It should not be calling *out* to anything to figure out the state of the environment.

Another thing to watch out for; if your object is using terms from multiple domains then it might be violating context independence rule. (Exception: it's part of a bridging layer(?))

The effects of this rule is on a system of objects is:

- it makes their relationships explicit and defined separately from the objects themselves
  - this simplifies the object; it doesn't need to manage it's own relationships
- makes it easier to manage the relationship since these objects are usually created and composed in the same places (factory objects)

### Hiding The Right Information

Encapsulation is *almost* always a good thing. It's not a good thing when things are hidden in the wrong place. It makes the code difficult to understand, integrate with, or build behaviour from composing objects. The advice the book gives to fight this is to reframe your reason for hiding something by saying "hide x inside this object".

For example:

- “Encapsulate the name of the application’s log file in the `PricingPolicy` class.”

vs

- “Hide the name of the application’s log file in the `PricingPolicy` class.”

In this example, the `PricingPolicy` class shouldn't need to care about the name of configuration files. If it does, then it should be passed in during runtime from some other class that does care about the name of external configuration files.