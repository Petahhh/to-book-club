# Chapter 9 - Commissioning an Auction Sniper

* Authors start to convey the experience of TDD in this chapter
* Show how techniques discussed so far fit together
* Tell a more realistic story, with backtrack on wrong decisions
* Recover from errors as soon as possible
* Emphasize the culture of very incremental development
* Keep things always clean and always working
* Focus in the one task at hand at once
* Merging code back in should never be a crisis

## To Begin at the Beginning

* Understand what are the client's needs
* Identify what are users trying to achieve
* Define basic terms for the project to prevent confusion on communications
* Gather requirements for the product being planned
* Build consensus with the user base
  * Define a goal
  * Start small
  * Build incrementally
  * Iterate until product reaches the goal

### Case study

* Client business largely depends on buying from auctions
* Buyers spend a lot of time doing manual work on auctions, such as checking state or deciding whether or not to bid
* End up missing attractive items because of not being quick enough
* Solution: build a system that automates this
* Need to integrate with a third-party online system

## Communicating with an Auction

* Message-based communication
* XMPP-based protocol
* Clients send commands for joining and bidding
* Server sends events for notifying the current accepted price and auction end

## Getting There Safely

* Even small applications are too large to write in a single go
* Figure out steps to get there by slicing up functionality
  * Significant and concrete enough that one can tell when it's done
  * Small enough to focus in one single concept and achieve quickly
* Small and coherent chunks
  * Help manage development risk
  * Allow better feedback
* Split into a series of incremental development steps
  * Each step adds a single element of complexity
  * Increments the work done so far
* Prioritize steps with users
  * Take needs into account
  * Define what is the minimum viable product
  * Create a plan to keep the team focused

## Well, This Isn't Real
* Overly simplified example, so we can address in the length of a book
* **Architecture is not realistic** but authors have seen similar things in real life
* **This isn't Agile planning**: we would have a view of the whole deliverable in a real-world project
* **This isn't realistic usability design**: good UX design investigates user needs to create a consistent experience
