## Composite Actions

### 1. Introduction: The Problem Composite Actions Solve

As workflows grow, duplication reappears — but in a different form.

Example:

Multiple workflows repeating:

- Setup steps
- Install dependencies
- Authentication logic
- Common scripts

Typical smell:

Same steps copied across jobs and workflows.

Problems created:

✔ Maintenance burden  
✔ Version drift  
✔ Hard debugging  
✔ YAML bloat  

Composite Actions solve this by enabling **step-level reuse**.

---

### 2. Actions vs Workflows — The Foundational Distinction

This confusion blocks many learners.

Clear separation:

Workflow = Orchestration layer  
Action = Reusable logic unit  

Mental model:

Workflow → Defines pipeline  
Action → Performs task  

---

### 3. Workflow Mental Model

Workflow:

✔ Contains jobs  
✔ Defines triggers  
✔ Controls execution  

Example:

on: push  
jobs: build, test, deploy  

---

### 4. Action Mental Model

Action:

✔ Reusable component  
✔ Encapsulates logic  
✔ Executed inside steps  

Example:

uses: actions/checkout@v4

---

### 5. Composite Action — What Exactly Is It?

Composite Action = Custom reusable bundle of steps.

Instead of repeating:

run commands  
setup logic  
scripts  

We define once → reuse everywhere.

Composite Action = Step abstraction mechanism.

---

### 6. Big Picture Mental Model

Without composite actions:

Workflow → Steps → Commands

With composite actions:

Workflow → Steps → Composite Action → Internal Steps

Layer of abstraction added.

---

### 7. Where Composite Actions Live

Composite actions live inside repository.

Typical structure:

.github/actions/my-action/action.yml

Important:

NOT inside workflows folder.

---

### 8. Basic Composite Action Structure

    File: .github/actions/setup-project/action.yml
    
    name: Setup Project
    description: Reusable setup logic
    
    inputs:
    	node-version:
    		required: true
    
    runs:
    	using: "composite"
    
    	steps:
    		- name: Setup Node
    		  uses: actions/setup-node@v4
    		  with:
    			node-version: ${{ inputs.node-version }}
    
    		- name: Install Dependencies
    		  run: npm install
    		  shell: bash

---

### 9. Critical Field: `runs.using: composite`

This line defines:

Action execution model.

Composite actions:

✔ Execute steps  
✔ Not JS code  
✔ Not Docker container  

---

### 10. Bundling Steps into Reusable Logic

Core purpose:

Encapsulate repeated step sequences.

Instead of repeating:

setup → install → configure

We reuse:

uses: ./.github/actions/setup-project

---

### 11. Calling Composite Action from Workflow

Workflow example:

    jobs:
    	build:
    		runs-on: ubuntu-latest
    		steps:
    
    			- name: Checkout Repo
    			  uses: actions/checkout@v4
    
    			- name: Setup Project
    			  uses: ./.github/actions/setup-project
    			  with:
    				node-version: 18

---

### 12. Critical Path Gotcha

Correct:

uses: ./.github/actions/setup-project ✔

Incorrect:

uses: setup-project ❌

Path must be exact.

---

### 13. Inputs — Making Actions Configurable

Inputs allow dynamic behavior.

Definition:
    
    inputs:
    	node-version:
    		required: true

Usage:

${{ inputs.node-version }}

---

### 14. Inputs Mental Model

Composite Action = Template  
Inputs = Parameters  

Same concept as reusable workflows.

---

### 15. Using Inputs Inside Steps

Example:

node-version: ${{ inputs.node-version }}

Inputs injected by caller workflow.

---

### 16. Outputs — Returning Data from Actions

Composite actions can expose outputs.

Example need:

Generate version → Use later.

---

### 17. Defining Outputs
    
    outputs:
    	short_sha:
    		description: Short commit hash
    		value: ${{ steps.generate.outputs.short_sha }}

---

### 18. Generating Outputs

    steps:
    	- name: Generate Output
    	  id: generate
    	  run: echo "short_sha=${GITHUB_SHA::7}" >> $GITHUB_OUTPUT
    	  shell: bash

---

### 19. Using Outputs in Workflow

    steps:
    	- name: Print Output
    	  run: echo "${{ steps.setup.outputs.short_sha }}"

---

### 20. Outputs Mental Model

Action → Produces values  
Workflow → Consumes values  

Composite actions enable step-level communication.

---

### 21. Shell Requirement Gotcha

Composite action steps require:

shell specification.

Example:

shell: bash

Without shell → Execution failure.

---

### 22. Why Composite Actions Are Powerful

They enable:

✔ DRY workflows  
✔ Logic reuse  
✔ Cleaner YAML  
✔ Centralized step logic  
✔ Easier updates  

---

### 23. Reusability Strategies

Composite actions shine when:

✔ Logic reused across workflows  
✔ Common setup patterns  
✔ Standard tooling steps  
✔ Repeated auth/config logic  

---

### 24. Real-World Use Cases

✔ Setup environments  
✔ Install dependencies  
✔ Standard build logic  
✔ Security checks  
✔ Deployment preparation  
✔ Notification wrappers  

---

### 25. Composite Action vs Reusable Workflow

Reusable workflow:

✔ Reuses entire workflow  
✔ Multiple jobs  

Composite action:

✔ Reuses steps  
✔ Single job scope  

Different abstraction layers.

---

### 26. Internal Automation Tooling Pattern

Very common in mature teams.

Pattern:

.github/actions/

Contains:

✔ setup-project  
✔ deploy-helper  
✔ lint-wrapper  
✔ security-scan  

Workflows become thin orchestrators.

---

### 27. Why This Pattern Scales Well

Benefits:

✔ Reduced duplication  
✔ Centralized logic  
✔ Easier maintenance  
✔ Cleaner workflows  
✔ Standardized pipelines  

---

### 28. Common Beginner Mistakes

Mistake 1:

Confusing composite action with workflow.

Mistake 2:

Wrong folder placement.

Mistake 3:

Forgetting `using: composite`.

Mistake 4:

Missing shell specification.

Mistake 5:

Incorrect input/output syntax.

---

### 29. Final Mental Model

Workflow = Pipeline orchestrator  
Composite Action = Reusable logic block  

Composite actions transform workflows from:

Large step collections

Into:

Modular automation systems.

---

### 30. Closing Insight

Beginner workflows duplicate steps.

Advanced workflows abstract them.

Composite actions are the first major step toward:

✔ Modular CI/CD design  
✔ Internal automation tooling  
✔ Scalable workflow architecture  

Mastering composite actions = CI/CD engineering maturity leap.
