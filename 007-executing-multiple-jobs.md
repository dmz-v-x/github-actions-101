## Executing Multiple Jobs

### 1. Introduction: Why Multiple Jobs Exist

In real-world automation, workflows rarely contain a single job.

Why?

Because different tasks have different concerns:

- Build application
- Run tests
- Lint code
- Deploy artifacts
- Send notifications

Combining everything into one job leads to:

- Slow pipelines
- Hard debugging
- Poor scalability
- Tight coupling

Multiple jobs solve this.

---

### 2. Big Picture Mental Model

Workflow  
	→ Jobs  
		→ Steps  

Key principle:

Jobs are **independent execution units**.

---

### 3. Example Workflow with Multiple Jobs
```
name: Multi Job Workflow

on: push

jobs:

	build_job:
		runs-on: ubuntu-latest
		steps:

			- name: Checkout Repo
				uses: actions/checkout@v4

			- name: Build Step
				run: echo "Building Application"

	test_job:
		runs-on: ubuntu-latest
		steps:

			- name: Checkout Repo
				uses: actions/checkout@v4

			- name: Test Step
				run: echo "Running Tests"
```
---

### 4. Understanding the Structure

Observe the hierarchy:

jobs:
	build_job:
	test_job:

Each job lives at the same level.

Not nested inside each other.

---

### 5. Critical Concept: Jobs Run in Parallel

By default:

Jobs run **simultaneously**.

Meaning:

build_job and test_job start at same time.

This is a major performance advantage.

---

### 6. Why Parallelism Matters

Single job pipeline:

Build → Wait → Test → Wait → Deploy

Multi-job pipeline:

Build + Test simultaneously

Result:

Faster workflows.

---

### 7. Job Isolation (Extremely Important)

Each job:

✔ Runs on separate runner  
✔ Has separate environment  
✔ Has separate filesystem  
✔ Does NOT share state  

Key implication:

Jobs are isolated machines.

---

### 8. Gotcha: Checkout Required in Each Job

Very common beginner mistake:

"I already checked out repo in build_job."

Incorrect assumption.

Jobs do NOT share workspace.

Each job must checkout repository.

---

### 9. Why Jobs Do Not Share Files

Because:

Each job = Fresh machine.

No memory between jobs.

---

### 10. `build_job` — Job Identifier

build_job:

Internal name.

Used for:

Dependencies & logs.

No functional magic.

---

### 11. Steps Inside Jobs

Each job contains:

steps:

Steps run sequentially within job.

Parallelism happens across jobs, not steps.

---

### 12. Runner Allocation per Job

runs-on: ubuntu-latest

Each job gets its own runner.

Meaning:

More jobs → More machines → More parallel execution.

---

### 13. Realistic Workflow Example
```
name: CI Pipeline

on: push

jobs:

	build:
		runs-on: ubuntu-latest
		steps:
			- uses: actions/checkout@v4
			- run: echo "Building"

	test:
		runs-on: ubuntu-latest
		steps:
			- uses: actions/checkout@v4
			- run: echo "Testing"

	lint:
		runs-on: ubuntu-latest
		steps:
			- uses: actions/checkout@v4
			- run: echo "Linting"
```
All three run in parallel.

---

### 14. Job Dependencies — Introducing `needs`

Sometimes parallelism is NOT desired.

Example:

Deploy only after build succeeds.

---

### 15. Example with Dependencies

deploy:
	needs: build
	runs-on: ubuntu-latest

Meaning:

deploy waits for build.

---

### 16. Dependency Mental Model

Without needs:

All jobs parallel.

With needs:

Execution graph formed.

---

### 17. Example Dependency Graph

build → test → deploy

test:
	needs: build

deploy:
	needs: test

---

### 18. Failure Propagation Behavior

If build fails:

✔ test never runs  
✔ deploy never runs  

Dependency chain stops.

---

### 19. Parallel + Dependency Mix

Possible design:

build → required first

test + lint → parallel after build

deploy → after both succeed

---

### 20. Example Mixed Workflow
```
jobs:

	build:
		...

	test:
		needs: build

	lint:
		needs: build

	deploy:
		needs: [test, lint]
```
Meaning:

deploy waits for BOTH.

---

### 21. Critical Gotcha: Dependency Syntax

needs: build ✔  
needs: [build] ✔  
needs: build, test ❌ Invalid  

Correct:

needs: [build, test]

---

### 22. Why Multiple Jobs Improve Debugging

Single job:

Long logs, mixed failures.

Multi-job:

Failures isolated.

You instantly know:

Build failed OR Test failed.

---

### 23. Scaling Advantage

Jobs allow:

✔ Horizontal scaling  
✔ Independent environments  
✔ OS-specific execution  

Example:

build → Linux  
test → Windows  
deploy → macOS  

---

### 24. OS Flexibility Example
```
jobs:

	build:
		runs-on: ubuntu-latest

	test:
		runs-on: windows-latest
```
---

### 25. Artifact Sharing Between Jobs

Jobs are isolated, BUT can share artifacts.

Example:

Build generates binary → Deploy uses it.

---

### 26. Artifact Pattern

build:
	- uses: upload-artifact

deploy:
	- uses: download-artifact

---

### 27. Common Beginner Mistakes

✔ Forgetting checkout per job  
✔ Assuming shared filesystem  
✔ Misusing needs syntax  
✔ Expecting sequential execution  
✔ Overloading single job  

---

### 28. Final Mental Model

Workflow = Orchestrator  
Jobs = Independent machines  
Steps = Sequential tasks inside job  

Default behavior:

Jobs → Parallel

Controlled behavior:

needs → Execution graph

---

### 29. Closing Insight

Understanding multiple jobs is where GitHub Actions starts feeling like a real CI/CD system.

From this point forward, workflows become:

Not just task lists…

But **execution graphs**.
