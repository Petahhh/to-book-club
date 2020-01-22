# Chapter 1 - Changing Software

- Changing code is good
- There are easy and hard ways
- Industry does not talk much about it
  - Closest: literature on refactoring

## Four Reasons to Change Software

1. Adding a feature
2. Fixing a bug
3. Improving the design
4. Optimizing resource usage

### Adding Features and Fixing Bugs

- Adding a feature might look like the most straightforward type of change
- Example: web based application
  - Manager wants to change logo position
  - Also wants to make logo animated for next release
  - Is this a new feature or a bug fix?
    - Depends on point of view
    - For developers, a new feature
    - For customers, might be a bug fix
  - Many organizations track new features separately from bugs
  - Discussion masks behavioural change
    - Big difference between adding new behaviour vs. changing old behaviour
  - Behaviour is the most important thing in software
    - Users depend on it
    - Adding new behaviour usually seen as good
    - Changing/removing behaviour users depend on breaks trust

### Improving Design

- Change structure to make code more maintainable
- Keep behaviours exactly the way they are
- If something is dropped in the process, it's usually called a bug
- Improving design without changing behaviour == _refactoring_
- If we have appropriate code coverage, we can make code maintainable without changing behaviour
- Differs from cleanup because changes are bigger and more risky
- Also is not invasive or risky as rewriting big chunks of code
- Refactoring is a series of structural modifications supported by tests to make code easier to change
- No functional changes

### Optimization

- Similar to refactoring but with a different goal
- Keeps functionality reducing resource consumption (time, memory, etc.)

### Putting It All Together

- Refactoring and optimization might look the same
- Most changes involve changing a small portion while maintaining most of the software the same
- Make sure the small number of things we change are changed correctly
- Preserving old behaviour might not be as easy
- Hard to know how much behaviour is at risk with every change

## Risky Change

- Make changes + preserve behaviour == risk
- Mitigate risks by asking questions:
  - What changes do we have to make?
  - How will we know that we've done them correctly?
  - How will we know that we haven't broken anything?
- How much change can you afford if changes are risky?
- Most teams have very conservative ways of managing this risk
  - Minimize number of changes to code base
  - Team policy: "If it's not broke, don't fix it"
  - Very cautious developers, afraid to change things
- Minimizing software problems by avoiding them is ineffective
- Fear of change leads to unmaintainable code
- Some onboarding time is natural
- Well structured code make you feel calm and confident
- Poorly structured code make it feels like jumping off a cliff to avoid a tiger
- Avoiding change also avoids practice, so you won't be as trained
- Sometimes teams live with so much fear they don't even realize it anymore
- Alternatives to fear of change:
  - Try harder
  - Hire more people to analyze and scrutinize code: more time + scrutiny make changes safer
  - How to know we've gotten it right?