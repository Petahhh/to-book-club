# Clean Code - Ch. 13: Concurrency

## Background:

* Concurrency is multiple computations running simultaneously
* It typically takes on two forms: **shared-memory** & **message-passing** paradigms
    * In the shared memory model of concurrency, concurrent modules interact by reading and writing shared objects in memory. 
    * In the message-passing model, concurrent modules interact by sending messages to each other through a communication channel. Modules send off messages, and incoming messages to each module are queued up for handling. 
* It affects **processes** & **threads**
    * Process is like a virtual computer; thread is like a virtual processor
* It causes **race conditions**
    * When correctness of result (postconditions and invariants) depends on the relative timing of events

These ideas influence good software in bad ways. Concurrency is necessary but it causes serious problems for correctness. 

## Guidelines:

* Code with concurrency is not safe from bugs. Concurrency bugs are some of the hardest bugs to find and fix, and require careful design to avoid. 
* Code with concurrency is rarely easy to understand. Predicting how concurrent code might interleave with other concurrent code is very hard for programmers to do. It’s best to design in such a way that programmers don’t have to think about that.  
* Solving concurrency issues requires decoupling what is done from when it is done.

This can both improve or complicate the structure, understandability, and efficiency of a system. 

Program state evolution, however, becomes much more complicated. Correct concurrency is complex, even for simple problems.

## Best practices:

1. Strictly obey the SRP (Single Responsibility Principle): Keep concurrency management separate from other code.
1. Severely limit access to data. Prefer copies over sharing and message passing style over threads using copies or sharing.
1. Know your library: thread-safe vs. non-thread-safe, blocking vs. non-blocking, executor framework, synchronization helpers.
1. Know basic concepts: bound resources, mutual exclusion, starvation, deadlock, livelock.
    * Bound resources: a way to think about backing services that makes them less prone to failure, and allows modularity (eg, in an emergency database outage, just bind the program to a different database) 
    * A mutual exclusion (mutex) is a program object that prevents simultaneous access to a shared resource. This concept is used in concurrent programming with a piece of code in front of processes or threads which access a shared resource. This is designed to prevent race conditions.
    * Deadlock is a situation where a set of processes are blocked because each process is holding a resource and waiting for another resource acquired by some other process.
    * Livelock occurs when two or more processes continually repeat the same interaction in response to changes in the other processes without doing any useful work. 
    * Starvation: In a dynamic system, requests for resources keep on happening. Thereby, some policy is needed to make a decision about who gets the resource when. This process, being reasonable, may lead to a some processes never getting serviced even though they are not deadlocked.
1. Know basic programming models: producer-consumer, reader-writer, dining philosophers situations.
1. Keep locked sections small. 
1. Graceful shutdown can be difficult (deadlocks).
1. Test with variations ("jiggling": #threads, speed, yielding, #processors, problem sizes, OSs, etc.) and track down each and every failure. Consider AOP-based instrumentation or tools for jiggling. Get non-threaded code working reliably first.
    * Author's note: this is a weird bit of the chapter for me, as there's no 'jiggle' in any of the testing frameworks I'm familiar with (for example) - it seems like it's Java- or some framework-specific. ¯\_(ツ)_/¯ 
