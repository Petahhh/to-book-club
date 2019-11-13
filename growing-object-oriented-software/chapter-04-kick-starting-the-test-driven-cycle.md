# Chapter 4 - Kick-starting the Test-Driven Cycle

core principles for this section:
* continuous incremental development
* expressive code

## Introduction

Your very first test:
* needs to fit into an automated testing framework
* should validate an actual valuable property of the system

This means integrating across its real external interfaces. For most
applications, getting this validation process automated amounts to completely
automating the build/deploy/test cycle up front.

## Walking Skeleton

* start from scratch, build a deployable system, deploy it into a
production-like environment and exercise the deployed system
* understand the whole delivery mechanism well enough to deploy it
* interface with other teams/upstream systems as soon as possible - this
generally involves some waiting/compromising and it pays to shift the pain as
far left as possible

## Uncertainty

* this is lean/agile - the requirements of your system are definitely gonna
change
* this is why you take your best guess, and actually have an automated means
of testing your hypotheses about what your system is supposed to do and what
it is supposed to integrate with
* again, front-loading the uncertainty and pain can save you a ton of trouble
later - XP practitioners know that it's much better/cheaper/smarter to learn
that a project is impossible or unnecessary before ever putting it in the hands
of customers!
