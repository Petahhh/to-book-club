# Chapter 4 - The Seam Model

Unless code was written with tests, it is often difficult to add them. We almost
always need to slightly change a program's behaviour in order to add helpful
tests to it.

If we model programs as _documents_, we tend to think that modifying their
source code is the only way to change their behaviour - but we have already
established that editing untested code is risky.

Thinking in terms of _seams_ offers a more fluent understanding of the ways a
program's behaviour is determined, in order to add tests without undue risk.

## Seams

**Seam**: a _seam_ is a place where you can alter behaviour in your program
without editing in that place. Every seam has an _enabling point_, a place where
you can make the decision to use one behaviour or another.

## Seam Types

Seams are language-specific.

### Object Seams

C++ example shows how you can change the behaviour of a line of code inside a
class that calls a global function by adding a virtual method to the class with
the same signature as the global function being called.

This is useful because it means we can create a subclass for the purpose of
testing. In our subclass we can  override the method we just introduced to
avoid calling a function that may involve a complex dependency or mutate some
shared global state - i.e. we can more effectively isolate the code we wish to
test.

Seam: method call

Enabling point: testing subclass' implementation

**Note:** not call method calls are seams - if the code both instantiates an
object and calls a method on the new instance, you cannot change the way
the method is dispatched. This is one of the reasons _dependency injection_ is a
common feature of well-tested OO code.

### Preprocessing Seams

In compiled languages with preprocessor macros, you can use tricks like 

```C++
#ifdef TESTING
#define function_with_side_effects()
	//mock implementation
#endif
```

Seam: call to `function_with_side_effects`

Enabling point: header file where the `#define` macro is used to replace it.

### Link Seams

In compiled languages with support for libraries - linking against other
pre-compiled programs - you can often change the behaviour of a program by
substituting a different library with an identical API.

Seam: use of a variable, type or function from a library

Enabling point: _outside_ the program text itself - the classpath or
build/deployment script. **Note:** this means these seams can be difficult to
notice.

### C++ Example

```C++
bool CAsyncSslRec::Init()
{
  ...
  if (!m_bFailureSent) {
    m_bFailureSent=TRUE;
    PostReceiveError(SOCKETCALLBACK, SSL_FAILURE);
  }
  ...
  return true;
}
```

The call to the global `PostReceiveError` function has multiple seams available:
1. _link_ - assuming the function is defined in a library, we can create
  a library with a stub function with the same signature and link to it during
  testing
1. _preprocessor_ - we can add an `#include` to a header that defines
  a stub implementation of `PostReceiveError` via a macro during testing.
1. _object_ - like the initial description, using a virtual `PostReceiveError`
  function on the `CAsyncSslRec` class.
