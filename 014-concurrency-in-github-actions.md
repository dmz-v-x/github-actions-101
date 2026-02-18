## Concurrency in GitHub Actions

### 1. Introduction: The Problem Concurrency Solves

Imagine this scenario:

You push code multiple times quickly.

push → workflow starts  
push again → workflow starts again  
push again → another workflow starts  

Now you have:

✔ Multiple builds running  
✔ Multiple deployments running  
✔ Possible race conditions  
✔ Wasted compute resources  

Worst case:

Old code finishes deploying after new code.

Concurrency exists to prevent this chaos.

---

### 2. What is Concurrency in GitHub Actions?

Concurrency controls:

How many workflow runs (or jobs) can execute simultaneously for a defined group.

Mental model:

Concurrency = Execution traffic controller

It decides:

✔ Run now  
✔ Cancel previous  
✔ Wait in queue  

---

### 3. Big Picture Mental Model

Without concurrency:

All triggered workflows run.

With concurrency:

Workflow runs become mutually controlled.

---

### 4. The Workflow We Are Analyzing

    name: CI/CD
    
    on:
    	push:
    		branches:
    			- main
    
    jobs:
    	build-and-deploy:
    		concurrency:
    			group: ${{ github.workflow }}-${{ github.ref }}
    			cancel-in-progress: true
    
    		runs-on: ubuntu-latest
    
    		steps:
    			- uses: actions/checkout@v3
    
    			- name: Build
    				run: echo "Building the app..."
    
    			- name: Deploy
    				run: echo "Deploying the app..."

---

### 5. Why Concurrency is Placed at Job Level

Concurrency can exist at:

✔ Workflow level  
✔ Job level  

Here:

concurrency inside job → Controls this job’s execution.

Important nuance:

Concurrency scope = Placement location.

---

### 6. Understanding the `group` Field

group: ${{ github.workflow }}-${{ github.ref }}

This line defines:

Which executions belong together.

---

### 7. Breaking Down the Expression

${{ github.workflow }}

Represents:

Workflow name.

Example:

CI/CD

---

${{ github.ref }}

Represents:

Git reference.

Example:

refs/heads/main

---

### 8. Combined Group Meaning

${{ github.workflow }}-${{ github.ref }}

Expands to something like:

CI/CD-refs/heads/main

Meaning:

✔ Same workflow  
✔ Same branch  
✔ Same concurrency group  

---

### 9. Critical Mental Model of Group

Group = Identity of mutual exclusion.

All runs with same group:

✔ Treated as related  
✔ Controlled together  

---

### 10. Why Include Branch in Group?

If group was only:

${{ github.workflow }}

Then:

main + dev + feature → All block each other.

Including branch allows:

✔ Branch-level isolation  

---

### 11. `cancel-in-progress: true` — The Most Important Line

cancel-in-progress: true

Meaning:

If new run starts → Cancel previous run in same group.

---

### 12. Execution Behavior with `true`

push → run starts  
push again → previous run canceled  
latest run executes  

Ensures:

Only newest execution survives.

---

### 13. Why This is Critical for Deployments

Prevents:

✔ Old code deploying  
✔ Race conditions  
✔ Conflicting releases  

Concurrency is a deployment safety mechanism.

---

### 14. `cancel-in-progress: false` Behavior

cancel-in-progress: false

Meaning:

Do NOT cancel previous runs.

Instead:

Queue new run.

---

### 15. Execution Behavior with `false`

push → run starts  
push again → queued  
push again → queued  

Runs execute sequentially.

---

### 16. Choosing Between `true` vs `false`

Use `true` when:

✔ Latest state matters  
✔ Deployments involved  
✔ Fast feedback desired  

Use `false` when:

✔ Every run important  
✔ No state replacement desired  
✔ Audit/logging critical  

---

### 17. Real-World Scenario Comparison

#### Scenario A — CI Builds

Multiple pushes → Only latest relevant.

Best choice:

cancel-in-progress: true

---

#### Scenario B — Scheduled Reports

Every execution matters.

Best choice:

cancel-in-progress: false

---

### 18. Critical Gotcha: Group Key Design

Poor group design causes:

✔ Unexpected cancellations  
✔ Unnecessary blocking  
✔ Workflow confusion  

Example mistake:

group: production

All workflows collide.

---

### 19. Advanced Group Strategies

Group by:

Workflow + Branch ✔  
Environment ✔  
Pull Request ✔  
Deployment Target ✔  

Example:

group: deploy-production

---

### 20. Workflow-Level Concurrency Example

    concurrency:
    	group: production-deployments
    	cancel-in-progress: true

Controls entire workflow runs.

Not just jobs.

---

### 21. Key Difference: Workflow vs Job Concurrency

Workflow-level:

Controls entire pipeline.

Job-level:

Controls specific job.

---

### 22. Common Beginner Mistakes

Mistake 1:

Misunderstanding group expressions.

Mistake 2:

Using overly broad groups.

Mistake 3:

Expecting concurrency to share data.

Mistake 4:

Confusing queue vs cancel behavior.

Mistake 5:

Ignoring branch impact.

---

### 23. Debugging Concurrency Behavior

If workflow canceled unexpectedly:

✔ Check group value  
✔ Check branch/ref  
✔ Check cancel-in-progress  

View logs for cancellation reason.

---

### 24. Performance & Cost Implications

Concurrency helps:

✔ Reduce wasted runs  
✔ Avoid duplicate deployments  
✔ Save compute minutes  

Especially important for:

Heavy builds / expensive jobs.

---

### 25. Final Mental Model

Concurrency = Execution control system.

Group = Defines mutual exclusion identity.

cancel-in-progress:

true → Replace previous  
false → Queue execution  

Without concurrency:

Execution chaos possible.

With concurrency:

Predictable automation behavior.

---

### 26. Closing Insight

Concurrency is not an optimization feature.

It is a **correctness and safety feature**.

Especially for:

✔ Deployments  
✔ Long-running workflows  
✔ Rapid commit environments  

Mastering concurrency = Essential CI/CD maturity milestone.
