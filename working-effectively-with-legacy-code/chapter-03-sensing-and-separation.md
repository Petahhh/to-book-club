# Sensing and Separation

Problem: We want to get tests in place but all these dependencies make it
REALLY difficult to write any tests without wiring up the entire system within
one test harness.

When we want to get tests in place there are two reasons to break dependencies:

1. Sensing: We break dependencies to *sense* when we can’t access values our code computes
2. Separation: We break dependencies to *separate* when we can’t even get a piece of code into a test harness to run

Here's an example to illustrate sensing and separation.

Here's a class called `NetworkBridge`:

```
public class NetworkBridge
{
  public NetworkBridge(EndPoint [] endpoints) {
    ...
  }

  public void formRouting(String sourceID, String destID) { 
    ...
  }
  ... 
}
```

`NetworkBridge` takes in an array of `Endpoints` and manages their
configuration using local hardware. Users of `NetworkBridge` can use its
methods to route traffic from one endpoint to another. `NetworkBridge` does this
work by changing settings on the `EndPoint` class. Each instance of the `EndPoint`
class opens a socket and communicates across the network to a particular
device.

Given this info, how would you go about trying to write tests for the
`NetworkBridge` class? Can we instantiate `NetworkBirdge` without hardware? How
does it interact with hardware when we call some of its methods? Similar
questions for its dependency, `Endpoint`. Overall `NetworkBridge` is a black box to us.

You could spend time debugging and figuring out how `NetworkBirdge` interacts
with hardware but it will be time consuming. We have no way to test drive this
one class to learn how it works.

This example illustrates our sensing and separation problem: we can't *sense*
the effect of our calls to methods on this class and we can't run it separately
from the rest of our application. These are the two main reasons why we break
dependencies.

Most common solution is to make fakes. More solutions to make in later chapters.

#### Making Fakes 101

- Create an interface that the dependency can implement
- Have the class you want to test depend on the interface instead of the external class
- Write a `FakeClass` that implements the interface you wrote
- In your tests you can now instantiate the class under test using the `FakeClass`

At this point some people argue that using fakes isn't really testing your
code. There is still value in this type of test though. These types of test can
help us narrow down where in our code bugs may be coming from by allowing us to
clearly see where the separation of concerns are between the different classes
in our application. (POS example in book page 26).

Mock Objects are another useful kind of fake. Mock objects allow you to make
assertions internally. 

Mocks generally work by you telling the mock what to expect. The mock will then
fail a test if it gets something unexpected.

Concourse uses mock HTTP servers in the fly test suite. We assert which calls
we expect fly to make when a certain command is executed and the mock http
server will fail a test if an incorrect series of HTTP calls are made.

