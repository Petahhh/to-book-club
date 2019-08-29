- author thinks the name is bad because it leads people to believe that "every module should do just one thing"
- Gets to a final definition of the Single Responsibility Principle (SRP) by doing the following:
		- SRP histoically defined as:
		
		> a module should have one, and only one, reason to change
	
	- states software changes because the users & stakeholders demand changes. They should be the "only reason" our software has to change. 
	
	- **Final definition**, substituting actors for "users and stakeholders":
	
	  > a module should be responsible to one, and only one, actor

- Now lets ask: what's a **module**?
  - could be a single source file
  - could be a set of cohesive functions and data structures
  - Final answer: it depends!

- Let's try and understand the SRP by violating it

### Symptom 1: Accidental Duplication

- I don't know why this symptom has *duplication* in its name as it's never mentioned in the chapter again...

You have the following `Employee` class from a payroll system:

```
Class Employee {
	func calcuatePay() int
	func reportHours() int
	func save()
}
```

- `calcuatePay()` is defined by the finance team
- `reportHours()` is defined by the HR team
- `save()` is defined the database admin (DBA) team

By having all these functions in one class we're coupling all three actors to each other.

The HR team could decide they want a `reportOverTimeHours()` function. This impacts the DBA team because the employee schema has to change to include an `overtime` column. 

The SRP states

> separate the code that different actors depend on

### Symptom 2: Merges

Two developers are changing the same module for two different reasons. 

As per previous example, say the HR team requests one change the finance team requests a separate change. Two developers start working on each issue separately, not aware they're both working on the same file. When they both go to merge one of them has to deal with the merge conflict and untangle the mess they've both created. 

### Solution

Separate the code!

```
Class PayCalculator {
	func Public CalculatePay()
}

Class HourReporter {
	func Public ReportHours()
}

Class EmployeeSaver {
	func Public SaveEmployee()
}
```

The goal of following SRP is that multiple actors should be able to influence your software without affecting the interests of the other actors.