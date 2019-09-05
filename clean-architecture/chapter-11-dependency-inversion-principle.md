# The Dependency Inversion Principle

tl;dr - depend on abstractions, not concretions.

a bit longer - depend on _stable_ abstractions, not _volatile_ concretions.

As referenced earlier in the book - the "inversion" part has to do with the fact
that the _source code dependency_ (i.e. which module imports which, or more
generally which refers to which in the code) runs opposite to the _control
dependency_ (i.e. when the runtime is executing code, any time it sees an
instance of an interface it will need to cast it down to its concrete
implementation in order to resolve method references, for example).

## Stable Abstractions

The language here is a bit interface-specific, whereas the more general notion
of "depending on abstractions" doesn't depend on the language construct of an
'interface'. The basic argument is that if class C implements interface I, you
are probably able to make significant changes to C and have it continue to meet
the contract required to implement I. Conversely, if you make noticeable changes
to I, then you are virtually guaranteed to need to change C (and any other
implementers of I).

This isn't a totally fair way to frame it, but if there's anything in your
system that you want to isolate from change, it will be your highly-reused
interfaces!

## Factories

```
ABSTRACT

    +-----------+                +---<I>-+
    |Application|--------------->|Service|
    +----+------+                +-------+
         |                           ^
         |                           |
         V              .......................
  +-----------<I>-+     .            |
  |Service Factory|     .     +------+------+
  +---------------+     .     |Concrete Impl|
  |  + makeSvc    |     .     +-------------+
  +---------------+     .            ^
         ^              .            |
         |              .            |
.........................            |
         |                           |
CONCRETE |                           |
         |                           |
+--------------------+               |
|Service Factory Impl|  <<creates>>  |
+--------------------+---------------+
|     + makeSvc      |
+--------------------+
```

## Concrete Components
