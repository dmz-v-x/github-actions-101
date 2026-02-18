## Event Filters in GitHub Actions

### 1. Introduction: Why Event Filters Exist

Workflows are triggered by events.

But triggering on every possible event is often undesirable.

Example problems without filters:

- Workflow runs too frequently
- Unnecessary compute usage
- Expensive pipelines triggered accidentally
- Deployments triggered at wrong times

Event filters allow precise control.

Mental model:

Event occurs → Filters applied → Workflow decision made

---

### 2. Big Picture Mental Model

Trigger = Event Source  
Filters = Execution Conditions  

Workflow runs only when:

Event + Filters match

---

### 3. General Event Configuration Structure

    on:
    	<event>:
    		types: [...]
    		branches: [...]
    		tags: [...]
    		paths: [...]

Each filter narrows execution.

---

### 4. Activity Types — The First Layer of Filtering

Some events have **activity types**.

Example:

pull_request event includes activities like:

- opened
- synchronize
- closed
- reopened

Without types filter:

Workflow runs on ALL activities.

---

### 5. Pull Request Activity Type Example

    on:
    	pull_request:
    		types: [opened, synchronize]

Meaning:

✔ Run when PR created  
✔ Run when PR updated  
✘ Do NOT run on close  

---

### 6. Why Activity Type Filtering Matters

Prevents:

✔ Unnecessary executions  
✔ Incorrect automation  
✔ Expensive operations  

Example:

Deploy only when PR merged (closed).

---

### 7. Issues Activity Type Example

    on:
    	issues:
    		types: [opened, closed, labeled]

Meaning:

✔ Run when issue created  
✔ Run when issue closed  
✔ Run when labels added  

---

### 8. Mental Model of Activity Types

Event → Multiple sub-events → types selects subset

---

### 9. Branch Filters — The Most Common Filter

Branch filters restrict workflow execution by branch.

Example:

    on:
    	push:
    		branches: [main]

Meaning:

✔ Run only on main  
✘ Ignore other branches  

---

### 10. Multiple Branch Example

    branches:
    	- main
    	- 'release/*'

Meaning:

✔ main branch  
✔ Any release branch  

---

### 11. Branch Pattern Matching

GitHub supports glob patterns:

* → Wildcard  
release/* → Matches release/x  

---

### 12. Branch Ignore Filters

    branches-ignore:
    	- dev
    	- 'feature/*'

Meaning:

✔ Skip these branches  

---

### 13. Critical Gotcha: branches vs branches-ignore

Using both together can be confusing.

Rules:

branches → Explicit inclusion  
branches-ignore → Explicit exclusion  

GitHub applies logic carefully.

---

### 14. Tags Filter — Version-Based Automation

    on:
    	push:
    		tags:
    			- 'v*'

Meaning:

✔ Run on version tags  

Common use case:

Release pipelines.

---

### 15. Paths Filter — File-Based Execution Control

Paths filter restricts execution based on changed files.

Example:

    paths:
    	- '**.js'

Meaning:

✔ Run only if JS files changed  

---

### 16. Path Ignore Example

    paths:
    	- '**'
    	- '!docs/**'

Meaning:

✔ Run on everything  
✘ Except docs  

---

### 17. Why Path Filters Are Critical

Prevents:

✔ Running CI for irrelevant changes  
✔ Wasted compute minutes  

Example:

Docs update → Skip heavy build.

---

### 18. Path Matching Mental Model

GitHub checks:

Files modified in commit.

Match → Workflow runs  
No match → Workflow skipped

---

### 19. Combining Branch + Path Filters

    on:
    	push:
    		branches: [main]
    		paths:
    			- '**.js'

Meaning:

✔ Only main branch  
✔ Only JS changes  

Both conditions must match.

---

### 20. Logical AND Behavior of Filters

Important rule:

Filters combine using AND logic.

Event + Branch + Path → Must all match.

---

### 21. Workflow Dispatch Filters

    workflow_dispatch:
    	inputs:
    		environment:
    			description: "Choose environment"

This is not filtering execution, but filtering input behavior.

Still part of trigger configuration.

---

### 22. Release Activity Types Example

    on:
    	release:
    		types: [published]

Meaning:

✔ Run only when release published  

Ignore edits, deletions.

---

### 23. Issue Comment Activity Example

    on:
    	issue_comment:
    		types: [created]

Meaning:

✔ Run only on new comments  

Ignore edits.

---

### 24. Why Activity Filters Prevent Chaos

Example without filter:

Comment edit → Workflow runs → Unexpected behavior.

Filters create predictability.

---

### 25. Common Beginner Mistakes

Mistake 1:

Assuming workflows trigger sequentially by file order.

Mistake 2:

Misunderstanding AND logic of filters.

Mistake 3:

Incorrect glob patterns.

Mistake 4:

Using wrong activity types.

Mistake 5:

Expecting workflow to run without matching paths.

---

### 26. Debugging Filter Issues

If workflow didn’t run:

Check:

✔ Event occurred?  
✔ Branch matched?  
✔ Paths matched?  
✔ Activity type matched?  

---

### 27. Important Gotcha: Paths Filter Requires File Changes

paths filter + empty commit → No execution.

Very common confusion.

---

### 28. Advanced Example Combining Everything

    on:
    	pull_request:
    		types: [opened, synchronize]
    		branches: [main]
    		paths:
    			- 'src/**'

Meaning:

✔ PR created or updated  
✔ Targeting main  
✔ Only src changes  

---

### 29. Final Mental Model

Event = Trigger source  
types = Activity filter  
branches = Branch filter  
tags = Tag filter  
paths = File filter  

Workflow runs ONLY when:

All filters match.

---

### 30. Closing Insight

Event filters transform workflows from:

Broad automation

Into:

Precisely targeted execution systems.

This is critical for:

✔ Performance optimization  
✔ Cost control  
✔ Deployment safety  
✔ CI/CD correctness  

Mastering filters = Essential GitHub Actions skill.
