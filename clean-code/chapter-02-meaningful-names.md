# Chapter 2 - Meaningful Names

## Use Intention-Revealing Names

Names should:
* Tell you **why** it exists
* **What** it does
* **How** it is used

> “If a name requires a comment, then the name does not reveal its intent”

Bad example:

```
int d; // elapsed time in days
```

Good example:

```
int elapsedTimeInDays; 
int daysSinceCreation;
```

Good names should be **explicit** about the meaning instead of relying on the reader to **imply** the meaning.

## Avoid Disinformation

* Avoid acronyms (single letter variable names like /i/ or /l/ can be confused as 0 and 1s
* Avoid naming multiple things similarly with subtle differences. It can be frustratingly confusing. Bad example:

```
XYZControllerForEfficientHandlingOfStrings
XYZControllerForEfficientStorageOfStrings
```

## Make Meaningful Distinctions

* Avoid using misspelling of names like “klass” for restricted words like “class”. The name should convey a distinct meaning if they must be different names.
* Avoid using naming such as “a1”, “a2”, … “aN” or “Info” and “Data” as they are not distinct 
* Avoid appending class type to a name or **adding noise**. Bad example:
```
NameString; // When are names ints?
CustomerObject; // What if we encounter just “Customer”
moneyAmount; // What’s distinct about this from just “money”?
theMessage; // “message” would be the same
```

## Use Pronounceable Names

* With names that can’t be pronounced - it’s harder to talk about the code and harder to read

## Searchable Names

* Single letter names make it hard to search through the code
  * Exception being a very small method with a local variable
* Having more lines of code is worth it if it makes the code more readable. For example extracting out a constant like in
`workDaysPerWeek = 5;`

## Avoid Encodings and Mappings
* Encodings such as “all data fields should be suffixed by ‘Readable’” or “all ints that are to be printed in red are suffixed by “Red” - this is a layer of knowledge the reader has to unnecessarily spend brain power to decode
* HungarianNotation and other older languages that had character limits required programmers to embed extra information into the variable names. It is not needed in modern programming
* Interfaces and Implementations: Encoding an interface is not great or necessary. If necessary encoding the implementation is better than the interface but neither should be necessary
* Try to use names that reflect either the problem domain or the solution. Avoiding both leads to poor naming.

>“clarity is king”

## Class & Method Names

* **Class names should be nouns**, not verbs. Avoid using “Manager”, “Processor”, “Data”, “Info”
* **Method names should be verbs**
* Accessors should be prefixed with **get**
* Mutators should be prefixed with **set**
* Predicates should be prefixed with **is**
* Overloaded constructors can be replaced with static factory methods that describe the arguments:
`Complex fulcrumPoint = Complex.fromRealNumber(23);`
* Don’t be cute. Naming things after slang or cultural references aren’t great.
>“Choose clarity over entertainment value”
* **Pick one word per concept:** A consistent lexicon is helpful for future readers of your code instead of “fetch” “retrieve” and “get” for a similar action on different classes
  * **Avoid puns:** Using the same word twice for two different meanings. It may be tempting to use the word “add” for multiple objects - but this can be bad if adding is handled differently underneath. Ex insert and append are both like add but distinct
* Using solution domain names is great because future readers of your code will also be programmers. Using problem domain names is good when there are no programmer related terms that fit well and especially more useful when the code is closer to the problem space.
* **Add meaningful context:** sometimes adding context to names (prefix or suffix) is helpful when the names in isolation may be confusing or not obvious. For example “street”, “housenumber” and “state” together may make sense but “state” on it’s own is not as obvious. Restructuring (wrapping things in a class or otherwise) may be a necessary step to make the context of names more clear.  
  * Warning: don’t add unnecessary context especially if many names end up being prefixed/suffixed with the same word. Example if a module is called ShoppersDrugMart, prefixing all the methods with SDM is unnecessary.

