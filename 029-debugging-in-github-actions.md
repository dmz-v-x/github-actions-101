## Debugging in GitHub Actions

### 1. Introduction: Why Debugging Skills Define CI/CD Mastery

Most workflow failures are NOT caused by GitHub.

They are caused by:

✔ Incorrect assumptions  
✔ Environment differences  
✔ Missing dependencies  
✔ Misconfigured logic  

Beginner reaction:

"Why is GitHub Actions broken?"

Advanced reaction:

"What exactly is happening inside the runner?"

Debugging is the real CI/CD superpower.

---

### 2. Critical Debugging Mental Model

Workflow failure ≠ Random error

Failure = Observable system behavior

Your job:

Extract signals from logs & environment.

---

### 3. Big Picture Debugging Strategy

When pipeline fails:

Step 1 → Observe logs  
Step 2 → Identify failure layer  
Step 3 → Inspect environment  
Step 4 → Reproduce issue  
Step 5 → Apply targeted fix  

---

## PART 1 — Debug Logging

---

### 4. Enabling Debug Logs (Critical Feature)

GitHub supports debug logging.

Enable via repository secrets:

ACTIONS_STEP_DEBUG → true

---

### 5. How to Enable Step Debug Logs

GitHub UI:

Repository → Settings → Secrets → Actions → New secret

Name:

ACTIONS_STEP_DEBUG

Value:

true

---

### 6. What Debug Logging Provides

✔ Extra execution details  
✔ Internal step behavior  
✔ Expression resolution  
✔ Action diagnostics  

Useful for mysterious failures.

---

### 7. Practical Debug Logging Example
    
    steps:
    	- name: Debug GitHub Context
    	  run: echo "${{ toJson(github) }}"

Purpose:

Inspect runtime metadata.

---

## PART 2 — Step Debugging Techniques

---

### 8. Logging Variables Safely

Common technique:

    run: |
    	echo "Branch: ${{ github.ref }}"
    	echo "Actor: ${{ github.actor }}"

Why this matters:

Validates assumptions.

---

### 9. Expression vs Shell Debugging

Incorrect assumption:

Shell variables automatically populated.

Correct debugging:

echo "${{ matrix.node-version }}"

---

### 10. Dumping Full Contexts

Very powerful diagnostic strategy:

echo "${{ toJson(matrix) }}"
echo "${{ toJson(runner) }}"

Reveals:

✔ Active configuration  
✔ Runner environment  
✔ Hidden mismatches  

---

## PART 3 — Inspecting Runner State

---

### 11. Why Runner Inspection Matters

Runner = Fresh machine.

Your local environment ≠ Runner environment.

---

### 12. Inspect Filesystem State
    
    steps:
    	- name: Inspect Files
    	  run: |
    		pwd
    		ls -la

Answers:

✔ Where am I?  
✔ What files exist?  

---

### 13. Inspect Installed Tools
    
    run: |
    	node -v
    	npm -v
    	python --version

Detects:

✔ Version mismatches  
✔ Missing tools  

---

### 14. Inspect Environment Variables

run: printenv

Useful for diagnosing:

✔ Missing config  
✔ Unexpected values  

---

## PART 4 — Re-run Strategies

---

### 15. Why Re-run Matters

Failures may be:

✔ Transient  
✔ Network-related  
✔ Dependency-related  

Re-run confirms stability.

---

### 16. GitHub Re-run Options

GitHub UI:

✔ Re-run all jobs  
✔ Re-run failed jobs  

Use when:

✔ External systems unstable  
✔ Suspected flakiness  

---

### 17. Re-run Mental Model

Re-run = Diagnostic tool

NOT permanent fix.

---

## PART 5 — Common Failure Diagnosis

---

### 18. Failure Layer Identification Model

Failures typically occur in:

✔ YAML syntax  
✔ Action resolution  
✔ Dependency installation  
✔ Command execution  
✔ Environment mismatch  

---

### 19. YAML Errors

Symptoms:

Workflow fails instantly.

Diagnosis:

✔ Check indentation  
✔ Check field placement  

---

### 20. Action Resolution Errors

Symptoms:

"Action not found"

Diagnosis:

✔ Path correctness  
✔ Version correctness  

---

### 21. Dependency Errors

Symptoms:

"Command not found"

Diagnosis:

✔ Missing install step  
✔ Runner tool availability  

---

### 22. Environment Mismatch Errors

Symptoms:

Works locally, fails in CI.

Diagnosis:

✔ Version logging  
✔ OS differences  
✔ Path differences  

---

### 23. Networking Errors

Symptoms:

Connection refused / timeout.

Diagnosis:

✔ Service container readiness  
✔ Port mapping  
✔ Health checks  

---

## PART 6 — Practical Hands-On Debug Workflow

---

### 24. Debugging Workflow Example

    name: Debug Workflow
    
    on: workflow_dispatch
    
    jobs:
    	debug:
    		runs-on: ubuntu-latest
    
    		steps:
    			- name: Dump GitHub Context
    			  run: echo "${{ toJson(github) }}"
    
    			- name: Dump Runner Context
    			  run: echo "${{ toJson(runner) }}"
    
    			- name: Inspect Filesystem
    			  run: |
    				pwd
    				ls -la
    
    			- name: Inspect Tools
    			  run: |
    				node -v
    				npm -v
    
    			- name: Inspect Environment
    			  run: printenv

---

### 25. Why This Workflow Is Powerful

Provides:

✔ Full system visibility  
✔ Environment clarity  
✔ Context verification  
✔ Rapid diagnosis  

---

## PART 7 — Practical Debugging Mental Models

---

### 26. Mental Model 1: "Assumptions Fail First"

Most failures caused by incorrect assumptions:

✔ File existence  
✔ Tool versions  
✔ Branch context  

Always log reality.

---

### 27. Mental Model 2: "Runner is Stranger Machine"

Never assume runner resembles your laptop.

Always verify environment.

---

### 28. Mental Model 3: "Logs Are Primary Source of Truth"

Errors rarely lie.

Interpret logs carefully.

---

### 29. Mental Model 4: "Failure = Signal, Not Obstacle"

Failures reveal:

✔ Missing dependencies  
✔ Incorrect logic  
✔ Hidden coupling  

---

### 30. Mental Model 5: "Debugging is Observability Engineering"

You are not just fixing YAML.

You are analyzing distributed automation behavior.

---

### 31. Common Beginner Mistakes

Mistake 1:

Blind trial-and-error edits.

Mistake 2:

Ignoring logs.

Mistake 3:

Not logging environment.

Mistake 4:

Misunderstanding contexts.

Mistake 5:

Treating transient failures as deterministic.

---

### 32. Final Mental Model

Debugging workflow = Observing system state

Key tools:

✔ Logs  
✔ Context dumps  
✔ Environment inspection  
✔ Re-run validation  

---

### 33. Closing Insight

GitHub Actions mastery is NOT syntax mastery.

It is:

✔ Failure diagnosis skill  
✔ Environment reasoning  
✔ Observability thinking  
✔ System debugging mindset  

Advanced users don’t fear pipeline failures.

They use them as diagnostic instruments.
