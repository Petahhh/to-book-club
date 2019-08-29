# Chapter 4 - Structured Programming

* Dijkstra recognized programming contained too much detail for the human brain to manage
* He wanted to bring a mathematical approach to programming so programmers could _prove_ their programs were correct
* Sometimes the use of `goto` is bad because it doesn't enable recursion and breaking things into smaller and easier to concieve pieces
* Sometimes the use of `goto` is great for enabling recursion. These uses include selection, iteration and control instructions such as if/then/else and do/while.
* Bohm and Jacopini proved all programs can be consutrctured from just these three structures:
  * sequence
  * selection
  * iteration
  
This is the foundation of a structured programming language. With these structures all code can be **proven** correct like in math proofs.

## Functional Decomposition

* Structured programming allows a program to be decomposed into many modules which themselves can be broken down into many small functions each which can be proven correct. 
* In real life, no one writes proofs for their code

## Science to the rescue

* Scientific theories and law cannot be proven like in math
> They are falsifiable but not provable
* Science works by proving statements false and the ones we can't prove false we take to be true

## Tests

* Tests are more the scientific method than mathematical.
* Tests help us find bugs but don't tell us that our methods are correct, only in that we have high confidence they are correct.
>If test fail to prove incorrectness, then we deem the functions to be correct enough for our purposes

## Conclusion

Software architects need to apply the above concept at scale. They should be breaking up software into components that are easily testable.
