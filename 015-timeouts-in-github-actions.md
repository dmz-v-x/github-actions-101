## Timeouts in GitHub Actions

### 1. Introduction: The Problem Timeouts Solve

Workflows do not always behave nicely.

Sometimes jobs:

- Hang indefinitely
- Wait on stuck processes
- Freeze due to network issues
- Deadlock on external systems
- Consume compute minutes forever

Without safeguards:

✔ Runners remain occupied  
✔ Compute costs increase  
✔ Pipelines block  
✔ Debugging becomes painful  

Timeouts exist to prevent infinite execution.

---

### 2. What is a Timeout in GitHub Actions?

A timeout defines:

Maximum allowed execution time.

If exceeded:

GitHub forcibly terminates the job.

Mental model:

Timer starts → Job runs → Timer expires → Job killed

---

### 3. Critical Mental Model

Timeout = Safety cutoff, NOT performance tuning

It prevents runaway executions.

---

### 4. Where Can Timeouts Be Defined?

Timeouts can be applied at:

✔ Job level  
✔ Step level (indirectly via scripts/tools)

Primary mechanism:

timeout-minutes

---

### 5. Basic Timeout Example

    name: Timeout Demo
    
    on: push
    
    jobs:
    	build:
    		runs-on: ubuntu-latest
    		timeout-minutes: 10
    
    		steps:
    			- name: Simulated Task
    				run: sleep 600

---

### 6. Understanding `timeout-minutes`

timeout-minutes: 10

Meaning:

✔ Job may run up to 10 minutes  
✔ After 10 minutes → Terminated  

Unit:

Minutes (integer only)

---

### 7. What Happens When Timeout is Reached?

GitHub:

✔ Stops runner execution  
✔ Marks job as failed  
✔ Logs timeout error  

Important nuance:

Timeout = Failure state

---

### 8. Why Timeouts Are Essential

Prevents:

✔ Infinite loops  
✔ Stuck builds  
✔ Frozen deployments  
✔ Resource exhaustion  

Timeouts = CI/CD safety mechanism

---

### 9. Realistic Hanging Scenario

Example problem:

run: npm install

If registry stalls:

✔ Command hangs  
✔ Job never completes  

Timeout protects pipeline.

---

### 10. Timeout vs Cancellation (Important Distinction)

Timeout:

✔ System-enforced  
✔ Based on duration  

Cancellation:

✔ User or concurrency-enforced  
✔ Based on events  

---

### 11. Job-Level Timeout Behavior

timeout-minutes applies to:

Entire job lifecycle.

Includes:

✔ All steps  
✔ Setup time  
✔ Execution time  

---

### 12. Critical Gotcha: Default Timeout Exists

GitHub-hosted runners have default timeout:

360 minutes (6 hours)

Many beginners do not know this.

Meaning:

Long jobs eventually stop even without explicit timeout.

---

### 13. Why Define Custom Timeouts?

Better control.

Example:

✔ Builds should finish in 15 minutes  
✔ Tests should finish in 5 minutes  
✔ Deployments should finish in 10 minutes  

Detect anomalies early.

---

### 14. Performance vs Timeout Relationship

Timeout is NOT optimization.

Slow job ≠ Timeout problem.

Timeout only guards extreme cases.

---

### 15. Example: Protecting Risky Step

    jobs:
    	risky_job:
    		runs-on: ubuntu-latest
    		timeout-minutes: 5
    
    		steps:
    			- name: External API Call
    				run: curl https://slow-api.com

Protects against network stalls.

---

### 16. Step-Level Timeout Strategy (Indirect)

GitHub does not provide native:

step timeout-minutes

Instead:

Use shell tools.

Example:

run: timeout 30s some-command

Linux utility.

---

### 17. Example Step Timeout

run: timeout 60s npm install

Meaning:

✔ Kill command after 60 seconds  
✔ Job continues or fails based on result  

---

### 18. Timeout Failure Symptoms

Logs show:

The job running longer than allowed timeout.

Understanding this prevents confusion.

---

### 19. Choosing Proper Timeout Values

Too short:

✔ False failures  

Too long:

✔ Slow feedback  
✔ Wasted resources  

Timeouts require realistic estimation.

---

### 20. Real-World Timeout Design Examples

Build job:

timeout-minutes: 15

Test job:

timeout-minutes: 10

Deploy job:

timeout-minutes: 5

---

### 21. Timeout Interaction with Dependencies

If job times out:

✔ Marked as failed  
✔ Downstream jobs skipped  

Failure propagation applies.

---

### 22. Timeout Interaction with Concurrency

Concurrency may cancel job earlier.

Whichever occurs first:

✔ Timeout expiry  
✔ Cancellation event  

---

### 23. Common Beginner Mistakes

Mistake 1:

Confusing timeout failure with logic failure.

Mistake 2:

Setting unrealistically low timeout.

Mistake 3:

Ignoring external dependency delays.

Mistake 4:

Expecting step-specific timeout behavior.

Mistake 5:

Misinterpreting termination logs.

---

### 24. Debugging Timeout Failures

Ask:

✔ Did job genuinely hang?  
✔ Was timeout too aggressive?  
✔ External system slow?  
✔ Infinite loop present?  

Timeout failures are diagnostic signals.

---

### 25. Cost & Resource Implications

Timeouts help:

✔ Reduce wasted compute minutes  
✔ Free stuck runners  
✔ Stabilize CI/CD pipelines  

Especially important for:

✔ Large teams  
✔ Expensive workloads  

---

### 26. Best Practices Summary

✔ Always define timeouts for critical jobs  
✔ Use realistic thresholds  
✔ Protect external operations  
✔ Combine with retries if needed  
✔ Treat timeouts as safety guards  

---

### 27. Final Mental Model

timeout-minutes = Maximum job runtime

Exceeded → Forced termination → Job fails

Timeouts prevent:

✔ Infinite execution  
✔ Pipeline deadlocks  
✔ Resource exhaustion  

---

### 28. Closing Insight

Timeouts are not optional safeguards.

They are fundamental for:

✔ Reliable automation  
✔ Predictable pipelines  
✔ Cost control  
✔ CI/CD stability  

Mature workflows always define execution boundaries.
