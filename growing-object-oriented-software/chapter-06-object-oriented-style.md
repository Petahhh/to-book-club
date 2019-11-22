# Chapter 6 - Object-Oriented Style

> Always design a thing by considering it in its next larger context - a chair in a room, a room in a house, a house in an environment, an environment in a city plan.
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

Objects communicate by sending and receiving messages through API's they provide to each other. Choosing how to communicate is very important, it determines how easy it is to use an object. If your API exposes too much of what you do (lack of information hiding) then objects that use you will probably end up implementing some of the work that your object does already. This makes it harder for your object to change and if it does change it may have ripple effects on its dependencies.
