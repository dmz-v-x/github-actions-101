## Executing Jobs Sequentially

### 1. Introduction: Sequential Jobs — Why Do We Need Them?

By default, GitHub Actions runs jobs in parallel.

But many real-world pipelines require strict ordering.

Examples:

- Build must complete before tests
- Tests must pass before deployment
- Deployment must finish before notifications

Parallel execution would break these flows.

This is where **sequential job execution** becomes critical.

---

### 2. Default Behavior Recap (Very Important)

Without dependencies:

All jobs run simultaneously.

This is often misunderstood.

Many beginners assume:

Jobs run top-to-bottom.

They do not.

---

### 3. How Do We Force Job Sequence?

GitHub Actions provides:

needs

This keyword creates execution dependencies.

Mental model:

needs = "Wait for this job to finish"

---

### 4. Example: Sequential Jobs Workflow
```
name: Sequential Workflow

on: push

jobs:

	build:
		runs-on: ubuntu-latest
		steps:

			- name: Checkout Repo
				uses: actions/checkout@v4

			- name: Build Step
				run: echo "Building Application"

	test:
		needs: build
		runs-on: ubuntu-latest
		steps:

			- name: Checkout Repo
				uses: actions/checkout@v4

			- name: Test Step
				run: echo "Running Tests"

	deploy:
		needs: test
		runs-on: ubuntu-latest
		steps:

			- name: Deployment Step
				run: echo "Deploying Application"
```
---

### 5. Understanding the Execution Graph

This workflow does NOT run linearly by file order.

It runs by dependency graph:

build → test → deploy

This is the core concept.

---

### 6. `needs: build` — What It Really Means

test:
	needs: build

Meaning:

✔ test waits for build  
✔ test starts only after build finishes  
✔ If build fails → test skipped  

Important nuance:

Skipped ≠ Failed.

---

### 7. Dependency Mental Model

Think of jobs as nodes:

build → prerequisite  
test → depends on build  
deploy → depends on test  

Workflow = Directed graph

---

### 8. Failure Propagation Behavior

Critical rule:

If dependency fails → Downstream jobs do not run.

Example:

build fails ❌

Result:

✔ test never starts  
✔ deploy never starts  

---

### 9. Why This Behavior Exists

Prevents:

- Invalid deployments
- Wasteful execution
- Cascading errors

Sequential pipelines protect integrity.

---

### 10. Checkout Gotcha Revisited

Even in sequential execution:

Jobs remain isolated.

Each job needs checkout.

Why?

Dependency controls timing, NOT filesystem sharing.

---

### 11. Jobs Are Still Separate Machines

Even when sequential:

✔ Separate runners  
✔ Separate environments  
✔ Separate workspace  

needs does NOT merge environments.

---

### 12. Visualizing Execution Timing

Without needs:

build + test + deploy → Parallel

With needs:

build → wait → test → wait → deploy

---

### 13. Sequential Chains vs Parallel Groups

Sequential chains:

A → B → C → D

Parallel groups:

A → [B + C + D]

Mixed pipelines:

A → [B + C] → D

---

### 14. Example: Partial Sequential Execution
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

✔ build first  
✔ test + lint parallel  
✔ deploy last  

---

### 15. Multiple Dependencies Syntax

Valid:

needs: build  
needs: [build]  
needs: [build, test]

Invalid:

needs: build, test ❌

Correct syntax matters.

---

### 16. Accessing Dependency Outputs

Advanced but important.

Jobs can expose outputs.

Dependent jobs can consume them.

Example concept:

build → produces version  
deploy → uses version  

---

### 17. Dependency Outputs Mental Model

needs = Timing + Data Access

Without needs:

No output access.

---

### 18. Common Beginner Mistakes

Mistake 1:

Expecting top-to-bottom execution without needs.

Mistake 2:

Forgetting checkout in dependent jobs.

Mistake 3:

Misusing needs syntax.

Mistake 4:

Expecting shared filesystem.

Mistake 5:

Misinterpreting skipped jobs as errors.

---

### 19. Skipped vs Failed Jobs

Failed:

Dependency ran → Error occurred.

Skipped:

Dependency failed → Job never executed.

This distinction is crucial for debugging.

---

### 20. Debugging Sequential Pipelines

If job didn’t run:

Check:

✔ Did dependency succeed?  
✔ Is needs defined correctly?  
✔ Any conditionals applied?  

---

### 21. Sequential Pipelines in Real Systems

Common production flow:

build → test → security scan → deploy → notify

Sequential execution ensures:

✔ Quality gates  
✔ Risk reduction  
✔ Stable releases  

---

### 22. Performance Tradeoff

Sequential execution:

✔ Safer  
✔ Slower  

Parallel execution:

✔ Faster  
✔ Less controlled  

Choosing correctly is architectural.

---

### 23. Final Mental Model

Default:

Jobs → Parallel

With needs:

Jobs → Dependency-driven sequence

Jobs remain:

✔ Isolated  
✔ Independent machines  

needs controls:

✔ Execution order  
✔ Failure propagation  
✔ Output access  

---

### 24. Closing Insight

Sequential jobs transform workflows from:

Simple task runners

Into:

Controlled automation pipelines with quality gates.

This is where GitHub Actions starts behaving like a true CI/CD engine.
