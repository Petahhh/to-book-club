- two primary ways to make changes in a system:
  - Edit and Pray
  - Cover and Modify

- Edit and Pray is the industry standard
  - requires carefully planning the changes you want to make, fully understanding the code you are modifying, and then making the changes. Once all changes are done you test to see if your changes work and didn't break anything else
  - On the surface this seems like "working with care". It **feels safe** because we did all the planning up front. It cannot be safe though if we don't use the right tools and techniques (more on what those are later)

- Cover and Modify means using a safety net when changing software
  - The safety net in this case is a cover that we put over the code we are working on. The cover/safety net in this case are a good set of tests around the code we are changing
  - With this safety net we can make changes carefully because the tests will provide us with a feedback loop about what we break as we make code changes

Author begins talking about testing in a traditional dev-and-QA environment, where dev has to ask QA to run tests on any changes they make and the feedback loop spans hours and potentially days. Author then introduces the reader to unit testing by describing how fast the feedback loop is when unit testing because you can run the entire unit test suite yourself in seconds.

- Stresses importance of testing in isolation vs larger integration type of tests. Both tests are important but there are a few problems you'd have if you only have integration tests to rely on:
  - Error localization: As a test gets further away from what it is testing it becomes harder to understand what caused a test failure. There's a similar problem with unit tests by the time it takes to pinpoint the cause is usually much shorter
  - Execution time: Integration tests take longer to execute because there's way more setup to do. Tests that take a long time to run generally don't get run at all (unless you're using an awesome CI system!)
  - Coverage: Can be hard to write these higher level tests and understand what parts of the code the test is covering

- Unit tests don't have the same problems as integration tests:
  - Error localization: You can make a one line change and run all your unit tests right after. If something breaks you already know the failure is due to the one line change you made
  - Execution time: unit tests run in miliseconds. Author stresses that a single unit test should take less than 1/10th of a second to run. If each individual test takes longer than 1/10th of a second then in aggregate the entire test suite will take several minutes and hours
  - Coverage: Can easily isolate a piece of code and test it under various conditions by changing the inputs

- Examples of tests that people may call **unit tests** but really aren't. Usually found in unit tests harnesses. These tests are still valuable, they don't help you make changes *fast* though
  - tests that talk to a database
  - tests that communicate over a network
  - tests that touch the file system
  - tests that require you to do special things to your environment (e.g. editing config files)


### Test Coverings

How do we start making changes to legacy code?

If we're lucky the legacy code will already have some unit tests. This makes it
easier to start making changes. In many cases there are no unit tests though
and trying to add tests are difficult since the legacy code was likely written
in a way that is not test friendly. Usually you run into dependency issues that
make it hard to write unit tests. The end result is that you may have to make
some changes to the classes you want to test without any safety net.

Are these changes safe to make. Author says **YES** if you use two techniques
that will be talked about later in the book: Primitivize Parameter and Extract
Interface. It's important to be conservative about these initial refactorings.

When breaking up legacy code the result can look **really** ugly. Some
dependencies break cleanly; others look less than ideal from a design point of
view. Once you have tests in place you can come back to these areas of code and
change their design to something cleaner.

#### Legacy Code Change Algorithm

1. Identify change points
2. Find test points
3. Break dependencies
4. Write tests
5. Make changes and refactor

The goal of following this algorithm is to make functional changes that deliver
value while bringing more of the system under test.

Identify Change Points: you need to know the design of your system really well
so you know where to make changes. More on this in later chapters.

Find Test Points: Making tests for legacy code is hard. More on this in later
chapters.

Break Dependencies: dependencies are often the largest impediment to testing.
Manifested in: 1) hard to instantiate objects in your test harnesses and 2)
difficulty in running methods within test harnesses. More details in later chapters.

Write Tests: Tests you write for legacy code look different compared to tests
for new code.

Make Changes And Refactor: author advocates for TDD. Remember that it's about
taking baby steps.
