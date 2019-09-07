# Chapter 13 - Component Cohesion

## REP: The Reuse/Release Equivalance Principle

> REP: The unit of reuse is the unit of release. Effective reuse requires tracking of releases from a change control system. The package is the effective unit of reuse and release.

(https://stackoverflow.com/a/63145)

> People who want to resuse software components cannot and will not do so unless those components are tracked through a release process and are given release numbers

* reusing code is one of the fulfilled promises of object oriented programming and we have so much available to us
* having release numbers enables programmers to become confident in compatibility of that module and their code
* having a release process that is transparent (notifications/good documentation) help programmers consume them (or not to consume for them for that release)
* releases should be cohesive - things in this should "go together" and "make sense"
  * this is a wishy-washy principle but in practice if you break it your users will respond
  
## CCP: The Common Closure Principle

> Gather into components those classes that change for the same reasons and at the same times. Separate into different components those classes that change at different times and for different reasons

* SRP but for components
* components should not have multiple reasons to change
* maintainability > reusability
* ideally a change in the system requires only a change in one component and therefore only one component needs to be redeployed
* this overlaps with the _closed_ part of the Open-Closed Principle: since 100% closure isn't possible, we can still be strategic about handling changes but keeping it _closed_ to one component, minizing cross-system redeployment

## CRP: The Common Reuse Principle

> Don't force users of a component to depend on things they don't need

* classes/modules within a component should be highly cohesive - a set of highly dependent classes should live in the same module
* on the flip side classes that are highly distinct should **not** be in a component together
* in summary: components should have **high class-to-class cohesion**

## Tension Diagram for Component Cohesion

```
+-----------+              Too many               +-----------+
|    REP    |          unneeded releases          |    CCP    |
| Group for | <---------------------------------> | Group for |
|  reusers  |                                     |Maintenance|
+-----------+                                     +-----------+
            ^                                     ^
            |                                     |
            |                                     |
            |                                     |
    Too many+-------->+-----------------+<--------+
    components        |       CRP       |          Hard to
    changing          | Split to avoid  |          reuse
                      |unneeded releases|
                      +-----------------+

```
