#  Chapter 8 - Building on Third Party Code

* we'll inevitably need to use third party code we don't control and therefore unable to influence it's design
* we can however focus our attention on integration: do we know how it works? what's the best balance between elegant integration and practicality?

## Only Mock Types That You Own

### Don't Mock Types You Can't Change

* it's not practical for us to deeply understand third party code: docs might be incorrect, their may be bugs and we have limited time and energy
* there is a high risk we do not correctly understand it
  * when the library is upgraded is it not easy to know if your understanding of it (via the mocks) is still correct
* even if we have access to the source, it's not practical to change it and stay up to date
* often time mocking objects you can't change leads to complexity in creating the correct scenarios - this complexity is not worth it

### Write an adapter layer

* adapter objects can be written from the perspective of our objects' domain
  * e.g. When writing adapter objects for the datadog api, the naming/language should be geared towards your design like `pushToDataStore` instead of `publishToPipeline` (n.b. `pipeline` is a datadog construct as well)
* adapters should be tested with integration tests (which are slower but okay because there shouldn't be that many of them)
* adapters should be as minimal as possible to minimize the about of brittle and hard to test code
* adapters help prevent implementation details of the third party api leaking into our code


```
               +--+  +--+    +--+  +--+
 App layer     |  |  |  |    |  |  |  |
               ++-+  +--+    ++-+  ++-+
                |    |        |     |
+-----------------------------------------------+
                |    |        |     |
Adapter layer  +v----v--+   +-v-----v--+
               |        |   |          |
               +--+-----+   +-+--------+
               |  |           |
+------------------------------------------------+
               |  |           |
3rd     +--+   | +v-+       +-v+
party   |  +<--+ |  |       |  |
layer   +--+     +--+       +--+

```

### Exceptions to mocking third party libraries

* when it's hard to trigger real life behaviour like throwing exceptions
* testing a sequence of calls (e.g. database transaction is reversed given a failure)
* _these should be kept to a minimum_

## Mock Applications Objects in Integration Tests

* when testing the application layer objects, the objective is to ensure when specific behaviour from the third party library happen, the result on the application side is what you expect (and vice versa) (e.g. the third party throws a http error code, you may expect the application object to get an error object raised that it knows how to handle)
* in this scenario you do want to mock the application objects


