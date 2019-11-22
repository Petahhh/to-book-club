# Chapter 6 - Object-Oriented Style

> Always design a thing by considering it in its next larger context - a chair in a room, a room in a house, a house in an environment, an environment in a city plan.
> - Eliel Saarinen

This chapter focuses on the attributes of the object oriented design we want to achieve. In the next chapter, we'll talk about how to achieve this design using TDD.

### Design for Maintainability

As code scales up, the only way we can continue to maintain an understanding of the system is by structuring the functionality into objects, obects into packages, packages into programs, and programs into systems.

Heuristics to guide this structuring:

- **Separation of concerns:** Gather togther code that will change for the same reason. When we need to change the behaviour of a system we want to change as little code as possible. If relevant changes are in the same area of the codebase it makes implementing the change easier.
- **Higher levels of abstraction:** Humans deal with complexity by avoiding complexity; humans work with higher levels of abstraction. When programming, combine useful components of functionality rather than manipulate variables and control flow.


