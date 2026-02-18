## Reusable Workflows in GitHub Actions

### 1. Introduction: The Hidden Problem in Growing CI/CD Systems

When projects are small, duplication is invisible.

One repository → One workflow → Everything manageable.

But real systems grow.

Example scenario:

- 10 repositories
- Each has CI workflow
- Each has build logic
- Each has test logic
- Each has deployment logic

Without noticing, teams create:

✔ 10 nearly identical YAML files  
✔ 10 places to update logic  
✔ 10 opportunities for inconsistency  

This is the **workflow duplication problem**.

Reusable workflows exist to solve this at scale.

---

### 2. The Duplication Problem Explained Clearly

Imagine updating:

Node version change  
Security patch  
Deployment logic  

Without reusable workflows:

You must edit every repository.

Problems created:

- Human errors
- Version drift
- Maintenance burden
- Configuration chaos

Reusable workflows introduce:

Centralized automation logic.

---

### 3. What is a Reusable Workflow?

Reusable workflow = Workflow designed to be called by other workflows.

Mental model:

Normal workflow → Triggered by events  
Reusable workflow → Triggered by other workflows

Instead of:

push → workflow runs

We use:

workflow_call → workflow runs

---

### 4. The Core Trigger: `workflow_call`

Reusable workflows use:

on:
	workflow_call:

This means:

"This workflow runs only when another workflow calls it."

No push.

No schedule.

No PR.

Only callable.

---

### 5. Basic Reusable Workflow Example

File: .github/workflows/reusable-ci.yml

    name: Reusable CI
    
    on:
    	workflow_call:
    
    jobs:
    	build:
    		runs-on: ubuntu-latest
    		steps:
    			- run: echo "Running reusable CI logic"

Key idea:

This workflow does nothing by itself.

It must be invoked.

---

### 6. Calling a Reusable Workflow

File: .github/workflows/main.yml

name: Main Workflow

    on:
    	push:
    
    jobs:
    	call-ci:
    		uses: ./.github/workflows/reusable-ci.yml

Mental model:

Workflow → Calls workflow → Jobs execute

---

### 7. Critical Path Gotcha

Correct path:

uses: ./.github/workflows/reusable-ci.yml ✔

Incorrect path:

uses: reusable-ci.yml ❌

Path must be exact.

---

### 8. Passing Inputs to Reusable Workflows

Reusable workflows become powerful when configurable.

Example need:

Different Node versions  
Different environments  
Different build modes  

Inputs solve this.

---

### 9. Defining Inputs in Reusable Workflow

    on:
    	workflow_call:
    		inputs:
    			node-version:
    				required: true
    				type: string

Meaning:

Caller must provide value.

---

### 10. Using Inputs Inside Workflow

steps:
	- run: echo "Node version is ${{ inputs.node-version }}"

Inputs accessed via:

${{ inputs.INPUT_NAME }}

---

### 11. Calling Workflow with Inputs

jobs:
	call-ci:
		uses: ./.github/workflows/reusable-ci.yml
		with:
			node-version: 18

Mental model:

Caller injects configuration → Reusable workflow adapts.

---

### 12. Inputs Mental Model

Reusable workflow = Template  
Inputs = Parameters  

---

### 13. Sharing Secrets Safely

Reusable workflows often require secrets:

API keys  
Tokens  
Credentials  

Secrets handling becomes critical.

---

### 14. Secrets in Reusable Workflow Definition

    on:
    	workflow_call:
    		secrets:
    			API_TOKEN:
    				required: true

Meaning:

Caller must pass secret explicitly.

---

### 15. Using Secret Inside Workflow

env:
	API_TOKEN: ${{ secrets.API_TOKEN }}

Secrets accessed via:

${{ secrets.SECRET_NAME }}

---

### 16. Passing Secret from Caller Workflow

    jobs:
    	call-ci:
    		uses: ./.github/workflows/reusable-ci.yml
    		secrets:
    			API_TOKEN: ${{ secrets.API_TOKEN }}

Critical security rule:

Secrets are NOT automatically inherited.

Must be passed explicitly.

---

### 17. Why Explicit Secret Passing Exists

Prevents:

✔ Accidental secret exposure  
✔ Over-privileged workflows  
✔ Security leaks  

---

### 18. Versioning Reusable Workflows (Extremely Important)

In large systems, workflows evolve.

Without versioning:

Changes may break dependent repositories.

---

### 19. Referencing Reusable Workflow by Version

uses: org/repo/.github/workflows/reusable-ci.yml@v1

Meaning:

Use version tag v1.

Benefits:

✔ Stability  
✔ Predictability  
✔ Controlled upgrades  

---

### 20. Versioning Mental Model

Reusable workflow = API  
Version = Contract  

---

### 21. Why Versioning is Critical

Without versioning:

Update reusable workflow → Break all callers.

With versioning:

Callers upgrade intentionally.

---

### 22. Cross-Repository Reusable Workflows

Reusable workflows can live in:

✔ Same repository  
✔ Different repository  

Cross-repo reference:

uses: org/shared-workflows/.github/workflows/ci.yml@v1

---

### 23. Centralized Automation Repository Pattern

Very common in real organizations.

Pattern:

shared-workflows repository

Contains:

✔ CI logic  
✔ Deployment logic  
✔ Security checks  
✔ Standard automation  

All repos reuse it.

---

### 24. Cross-Repo Standardization Benefits

✔ Single source of truth  
✔ Consistent pipelines  
✔ Easier maintenance  
✔ Reduced duplication  

---

### 25. Advanced Inputs + Secrets Example

Reusable workflow:

    on:
    	workflow_call:
    		inputs:
    			environment:
    				required: true
    				type: string
    
    		secrets:
    			DEPLOY_TOKEN:
    				required: true

Caller workflow:

    jobs:
    	deploy:
    		uses: org/shared/.github/workflows/deploy.yml@v2
    		with:
    			environment: production
    		secrets:
    			DEPLOY_TOKEN: ${{ secrets.DEPLOY_TOKEN }}

---

### 26. Common Beginner Mistakes

Mistake 1:

Expecting reusable workflow to run automatically.

Mistake 2:

Forgetting required inputs.

Mistake 3:

Forgetting secrets must be passed.

Mistake 4:

Incorrect path references.

Mistake 5:

Ignoring versioning.

---

### 27. Reusable Workflow vs Composite Action (Important Distinction)

Reusable workflow:

✔ Entire workflow logic  
✔ Multiple jobs possible  

Composite action:

✔ Step-level reuse  
✔ Single job scope  

Different abstraction layers.

---

### 28. Final Mental Model

Normal workflow → Triggered by GitHub events

Reusable workflow → Triggered by workflows

Reusable workflows enable:

✔ Centralization  
✔ Standardization  
✔ Scalability  
✔ Maintainability  

---

### 29. Closing Insight

Small projects survive duplication.

Large systems collapse under it.

Reusable workflows transform CI/CD from:

Repository-level automation

Into:

Organization-level automation architecture.

This is a major leap toward professional CI/CD design.
