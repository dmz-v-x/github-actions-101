## Self Hosted Runners

### 1. Introduction: The Limitation of GitHub-Hosted Runners

By default, GitHub Actions uses:

GitHub-hosted runners.

These are:

✔ Fresh virtual machines  
✔ Managed by GitHub  
✔ Convenient and simple  

But real-world systems often outgrow them.

Common problems:

- Need custom software
- Need private network access
- Need faster hardware
- Need long-running workloads
- Need cost optimization

This is where **self-hosted runners** become essential.

---

### 2. What is a Self-Hosted Runner?

Self-hosted runner = Your own machine executing workflows.

Mental model:

GitHub → Sends job → Your machine runs it

Instead of GitHub-managed infrastructure:

You manage the execution environment.

---

### 3. Critical Mental Model

GitHub Actions = Orchestrator  
Runner = Execution engine  

With self-hosted runners:

You control the engine.

---

### 4. When GitHub-Hosted Runners Are Insufficient

Self-hosted runners are useful when you need:

✔ Custom OS configuration  
✔ Pre-installed tools  
✔ Access to private resources  
✔ Specialized hardware  
✔ Cost optimization at scale  

---

### 5. Real-World Use Cases

✔ Private database access  
✔ Internal company APIs  
✔ GPU / high-performance builds  
✔ Legacy tooling  
✔ Enterprise security policies  

---

## HANDS-ON SETUP — Creating Your First Self-Hosted Runner

---

### 6. Big Picture Setup Flow

Step 1 → Prepare machine  
Step 2 → Register runner  
Step 3 → Connect to GitHub  
Step 4 → Run workflows  

---

### 7. Step 1: Prepare Machine

Requirements:

✔ Stable internet  
✔ Supported OS (Linux, Windows, macOS)  
✔ Sufficient permissions  

Example machine:

Ubuntu server / local VM / cloud instance.

---

### 8. Step 2: Navigate to Runner Settings

GitHub UI:

Repository → Settings → Actions → Runners → New self-hosted runner

You will see:

✔ OS selection  
✔ Download instructions  

---

### 9. Step 3: Download Runner Package

Example (Linux):

	mkdir actions-runner && cd actions-runner

	curl -o actions-runner-linux-x64.tar.gz -L <download-url>

	tar xzf ./actions-runner-linux-x64.tar.gz

Key idea:

Runner software installed locally.

---

### 10. Step 4: Configure Runner

GitHub provides tokenized command:

	./config.sh --url https://github.com/OWNER/REPO --token TOKEN_VALUE

This registers machine with GitHub.

Mental model:

Machine announces itself to GitHub.

---

### 11. Configuration Prompts Explained

During config:

✔ Runner name  
✔ Labels  
✔ Work directory  

Labels are extremely important.

---

### 12. Labels — Critical Scheduling Mechanism

Labels allow workflows to target runners.

Example labels:

self-hosted  
linux  
high-memory  
gpu  

Mental model:

Labels = Runner capabilities.

---

### 13. Step 5: Start Runner

	./run.sh

Runner now listens for jobs.

Machine becomes active execution node.

---

## USING SELF-HOSTED RUNNER IN WORKFLOW

---

### 14. Basic Workflow Example

name: Self Hosted Demo

    on: push
    
    jobs:
    	build:
    		runs-on: self-hosted
    
    		steps:
    			- name: Run on My Machine
    			  run: echo "Running on self-hosted runner"

---

### 15. Targeting Specific Runner via Labels

runs-on:
	- self-hosted
	- linux
	- high-memory

Ensures:

✔ Correct runner selection  

---

### 16. Runner Lifecycle — Critical Concept

Self-hosted runner lifecycle:

Idle → Receives job → Executes → Returns to idle

Unlike GitHub-hosted:

✔ Machine persists  
✔ State persists  
✔ Files may persist  

---

### 17. Persistence Gotcha

Self-hosted runners are NOT stateless.

Meaning:

✔ Files may remain  
✔ Cache may remain  
✔ Build artifacts may remain  

Requires cleanup strategy.

---

### 18. Cleanup Best Practice

Use explicit cleanup steps:

	rm -rf build/

Prevents contamination between runs.

---

## SCALING ARCHITECTURE

---

### 19. Why Scaling Matters

Single runner:

✔ Limited concurrency  

Multiple runners:

✔ Parallel execution  
✔ Higher throughput  

---

### 20. Scaling Strategies

✔ Multiple VMs  
✔ Cloud auto-scaling runners  
✔ Kubernetes runners  
✔ Ephemeral runners  

---

### 21. Ephemeral Runner Model

Runner created → Runs job → Destroyed

Benefits:

✔ Clean state  
✔ Better security  
✔ Predictable execution  

---

## SECURITY IMPLICATIONS (EXTREMELY IMPORTANT)

---

### 22. Critical Security Mental Model

Self-hosted runner = Your infrastructure exposed to workflows.

Workflows execute arbitrary code.

Security risk = Real.

---

### 23. Major Risks

✔ Malicious workflow execution  
✔ Secret exfiltration  
✔ Infrastructure compromise  
✔ Lateral movement inside network  

---

### 24. Security Best Practices

✔ Restrict repository access  
✔ Use least privilege permissions  
✔ Isolate runners  
✔ Avoid sensitive networks  
✔ Use ephemeral runners  

---

### 25. Fork Workflow Danger

Untrusted fork PR → Runs on self-hosted runner → Severe risk.

Always configure carefully.

---

## COST & PERFORMANCE TRADEOFFS

---

### 26. GitHub-Hosted Runners

✔ Simple  
✔ Predictable  
✔ No maintenance  

✘ Usage-based billing  
✘ Limited customization  

---

### 27. Self-Hosted Runners

✔ Full control  
✔ Performance tuning  
✔ Potential cost savings  

✘ Infrastructure maintenance  
✘ Security responsibility  

---

### 28. Performance Advantages

✔ Pre-installed dependencies  
✔ Faster hardware  
✔ Persistent caches  

Result:

✔ Massive CI speed improvements possible.

---

### 29. Cost Optimization Insight

At scale:

Heavy workloads → Self-hosted often cheaper.

Small workloads → GitHub-hosted simpler.

---

### 30. Common Beginner Mistakes

Mistake 1:

Ignoring security implications.

Mistake 2:

Assuming runners are stateless.

Mistake 3:

Poor label design.

Mistake 4:

No cleanup strategy.

Mistake 5:

Overexposing runners to untrusted workflows.

---

### 31. Final Mental Model

GitHub Actions = Brain  
Runner = Muscle  

GitHub-hosted → Rent muscle  
Self-hosted → Own muscle  

Self-hosted runners transform CI/CD into:

Automation + Infrastructure Engineering

---

### 32. Closing Insight

Self-hosted runners are not just a feature.

They represent a major architectural shift:

From using GitHub’s infrastructure…

To designing your own execution platform.

This is where CI/CD intersects with:

✔ DevOps  
✔ Cloud Engineering  
✔ Platform Engineering  
✔ Security Engineering  

Mastering runners = Advanced CI/CD capability.
