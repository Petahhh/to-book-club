# Chapter 2 - A Tale of Two Values

* Sofware engineers should be delivering two kinds of value: Behaviour and Architecture
  * *Behaviour*: Completing the spec and fixing bugs when the machine doesn't meet this spec. Some software engineers think this is the entirety of their jobs
  * *Architecture:* The _soft_ in software imply the machine's behaviour should be easy to change.
  
  > The difficulty in making such a change should be proportional only to the scope of the change, and not hte _shape_ of the change

As software is built, each additional new feature can get more and more expensive to build. Clean architecture allows for the difficulty of each new feature to be as consistent with previous features as possible. 

_What's more important? The Behaviour or Architecture?_

> If you give me a program that **works** perfectly but is impossible to change, then it won't work when the requirements change, and I won't be able to make it work. Therefore the program will be useless.

> If you give me a program that **does not work** but is easy to change, then I can make it work, and keep it working as a requirements change. Therefore the program will remain continually useful.

In summary: It's better to have readable easy to change code, with that you can make it correct.

\* _impossible to change_ is not a reality but sometimes code is "pratically impossible to change" because its costs outweigh the benefits

## Eisenhower's Matrix

|important urgent|important **not** urgent|
|---|---|
|urgent **not** important| **not** urgent **not** important|


> I have two kinds of problems, the urgent and the important. The urgent are not important and the important are never urgent - Eisenhower 1954

Behavior is urgernt but not always important.
Architecture is important but not always urgent.

The priority of the cells in the matrix goes as follows:

1. Urgent and important (Never happens)
1. Not urgrent and important (Architecture first!)
1. Urgernt and not important (Specs secondary!)
1. Not urgrent, not important (what would this be? insignificant refactors that are satisfying but don't make the code cleaner??)

> It is the responsibility of the software development team to assert the importance of architecture over the urgency of features.

## Fight for Architecture

* the fight for architecture is a *struggle* but you're also a stakeholder in the software you work on so you gotta protect it
* if architecture comes last - then the productivity of adding new features goes down eventually meaning the "software team did not fight hard enough for what they knew was necessary"

