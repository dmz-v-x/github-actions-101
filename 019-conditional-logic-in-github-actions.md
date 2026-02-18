## Conditional Logic in GitHub Actions

### 1. Introduction: Why Conditional Logic Exists

Not every step should always run.

Real workflows often require logic like:

- Run deployment only in production
- Run setup only for specific Node versions
- Run cleanup only on failure
- Skip expensive tasks when unnecessary

Without conditionals:

✔ Workflows become inefficient  
✔ Pipelines become rigid  
✔ Logic becomes duplicated  

The `if` keyword enables conditional execution.

---

### 2. What is an `if` Expression?

An `if` expression controls:

Whether a job or step executes.

Mental model:

Condition evaluated → true → Run  
Condition evaluated → false → Skip

---

### 3. Big Picture Mental Model

Workflow runs → GitHub evaluates expressions → Execution decisions made

Important:

Conditions are evaluated by GitHub, NOT the shell.

---

### 4. The Workflow We Are Analyzing

    name: Conditional Example
    
    on:
    	workflow_dispatch:
    		inputs:
    			environment:
    				description: "Environment"
    				required: true
    				default: "dev"
    
    jobs:
    	example:
    		runs-on: ubuntu-latest
    
    		strategy:
    			matrix:
    				node-version: [16, 18]
    
    		steps:
    
    			- name: Always Run
    			  run: echo "This always runs"
    
    			- name: Run only on Node 18
    			  if: matrix.node-version == '18'
    			  run: echo "Running special setup for Node 18"
    
    			- name: Run only in Production
    			  if: inputs.environment == 'prod'
    			  run: echo "This runs only when deploying to prod"
    
    			- name: Only on main branch
    			  if: github.ref == 'refs/heads/main'
    			  run: echo "Running on main branch"
    
    			- name: Fails intentionally
    			  run: exit 1
    
    			- name: Run on failure
    			  if: failure()
    			  run: echo "Previous step failed!"

---

### 5. Default Behavior Without `if`

Steps run unconditionally.

Equivalent to:

if: true

---

### 6. Step-Level vs Job-Level Conditionals

Conditionals can control:

✔ Entire jobs  
✔ Individual steps  

Here:

Each step independently evaluated.

---

### 7. Expression Evaluation Timing

Conditions evaluated:

✔ Before step execution  
✔ Using runtime contexts  

Not after command execution.

---

### 8. Matrix-Based Conditional

if: matrix.node-version == '18'

Meaning:

✔ True when Node = 18  
✔ False when Node = 16  

Result:

Node 16 job → Step skipped  
Node 18 job → Step runs  

---

### 9. Critical Gotcha: Data Type Confusion

Matrix values are strings.

Even if defined as numbers:

node-version: [16, 18]

Comparison requires:

'18' ✔  
18 ❌

---

### 10. Inputs-Based Conditional

if: inputs.environment == 'prod'

Meaning:

✔ True only when user selects prod  
✔ False otherwise  

---

### 11. Inputs Scope Gotcha

inputs only exist for:

workflow_dispatch

Using inputs in push workflow → Undefined.

---

### 12. GitHub Context Conditional

if: github.ref == 'refs/heads/main'

Meaning:

✔ True on main branch  
✔ False on other branches  

---

### 13. Common Branch Gotcha

github.ref ≠ github.ref_name

ref → Full path  
ref_name → Short name

Choose correctly.

---

### 14. Failure-Based Conditional

if: failure()

Special built-in function.

Meaning:

✔ Runs only if previous step failed  

---

### 15. Built-in Status Functions

success() → Previous steps succeeded  
failure() → Any previous step failed  
cancelled() → Workflow cancelled  
always() → Always runs  

---

### 16. Failure Propagation Flow

Fails intentionally:

run: exit 1

Result:

✔ Step fails  
✔ Job marked failure (unless handled)  
✔ failure() becomes true  

---

### 17. Why Failure Conditionals Are Powerful

Enable:

✔ Cleanup logic  
✔ Notifications  
✔ Rollback mechanisms  
✔ Debugging steps  

---

### 18. Important Gotcha: Skipped ≠ Failed

Condition false → Step skipped

Skipped step:

✔ Not failure  
✔ Does NOT trigger failure()  

---

### 19. Complex Logical Expressions

GitHub supports logical operators:

&& → AND  
|| → OR  
! → NOT  

Example:

if: github.ref == 'refs/heads/main' && matrix.node-version == '18'

---

### 20. Negation Example

if: inputs.environment != 'prod'

Runs everywhere except prod.

---

### 21. Always Execution Example

if: always()

Runs regardless of failure/cancellation.

Useful for:

✔ Cleanup  
✔ Artifact upload  
✔ Logging  

---

### 22. Common Beginner Mistakes

Mistake 1:

Forgetting quotes in comparisons.

Mistake 2:

Confusing shell syntax with expression syntax.

Mistake 3:

Using invalid context properties.

Mistake 4:

Expecting skipped steps to trigger failure().

Mistake 5:

Misinterpreting workflow results.

---

### 23. Expression vs Shell Variable Confusion

Incorrect:

if: $NODE_VERSION == 18 ❌

Correct:

if: matrix.node-version == '18' ✔

Expressions use contexts, not shell variables.

---

### 24. Conditional Debugging Strategy

Very common technique:

run: echo "${{ github.ref }}"

Helps validate condition logic.

---

### 25. Final Mental Model

if = Execution gate

Condition true → Run  
Condition false → Skip  

Conditions evaluated by:

GitHub expression engine

Not shell

---

### 26. Closing Insight

Conditionals transform workflows from:

Static pipelines

Into:

Logic-driven automation systems.

This enables:

✔ Smart CI/CD  
✔ Environment-aware execution  
✔ Failure-aware logic  
✔ Performance optimization  

Mastering `if` expressions = Major GitHub Actions milestone.
