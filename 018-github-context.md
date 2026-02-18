## GitHub Context

### 1. Introduction: What is a Context in GitHub Actions?

In GitHub Actions, workflows often need dynamic information:

- Which event triggered the workflow?
- Which branch is running?
- Who triggered the workflow?
- Which OS is being used?
- Which matrix configuration is active?

Hardcoding values would make workflows rigid.

Contexts solve this.

Context = Structured runtime data provided by GitHub.

Mental model:

Context = Live metadata about workflow execution

---

### 2. Big Picture Mental Model

Workflow runs → GitHub injects contexts → Expressions read contexts

Contexts allow workflows to behave dynamically.

Without contexts:

Automation would be static.

---

### 3. The Workflow We Are Analyzing

    name: All Contexts Demo
    
    on:
    	workflow_dispatch:
    		inputs:
    			environment:
    				description: "Choose the deployment environment"
    				required: true
    				default: "staging"
    
    jobs:
    	demo:
    		name: Demo All Contexts
    		runs-on: ${{ matrix.os }}
    		environment: ${{ inputs.environment }}
    
    		strategy:
    			fail-fast: false
    			max-parallel: 2
    
    			matrix:
    				os: [ubuntu-latest, windows-latest]
    				node-version: [16, 18]
    
    				include:
    					- os: ubuntu-latest
    					  node-version: 20
    					  extra: "experimental"
    
    				exclude:
    					- os: windows-latest
    					  node-version: 16
    
    		env:
    			GLOBAL_ENV_VAR: "I'm a global env variable"
    			BRANCH_NAME: ${{ github.ref_name }}
    
    		steps:
    			- name: Checkout Code
    			  uses: actions/checkout@v4
    
    			- name: Show GitHub Context
    			  run: |
    				echo "Event: ${{ github.event_name }}"
    				echo "Repository: ${{ github.repository }}"
    				echo "Actor: ${{ github.actor }}"
    				echo "Ref: ${{ github.ref }}"
    				echo "Branch: ${{ github.ref_name }}"
    				echo "Workflow: ${{ github.workflow }}"
    				echo "Job: ${{ github.job }}"

---

### 4. Expression Syntax Refresher

Contexts are accessed via:

${{ context.property }}

Example:

${{ github.actor }}

Important rule:

Expressions are evaluated by GitHub, NOT the shell.

---

### 5. GitHub Context — The Most Important Context

GitHub context provides metadata about:

✔ Repository  
✔ Event  
✔ Branch  
✔ Commit  
✔ Workflow  

---

### 6. Common GitHub Context Properties

#### github.event_name

Which event triggered workflow.

Examples:

push  
pull_request  
workflow_dispatch  

---

#### github.repository

Repository identifier.

Format:

owner/repo

---

#### github.actor

User who triggered workflow.

Critical for:

✔ Auditing  
✔ Conditional logic  

---

#### github.ref

Full Git reference.

Example:

refs/heads/main

---

#### github.ref_name

Short reference name.

Example:

main

Cleaner for display/logging.

---

#### github.workflow

Workflow name.

Useful for:

✔ Logging  
✔ Concurrency grouping  

---

#### github.job

Current job identifier.

Useful for debugging.

---

### 7. Runner Context — Machine Metadata

Runner context describes execution machine.

Example step:

echo "Runner OS: ${{ runner.os }}"

---

### 8. Common Runner Properties

#### runner.os

Operating system.

Examples:

Linux  
Windows  
macOS  

---

#### runner.name

Runner identifier.

Useful in self-hosted setups.

---

#### runner.arch

Machine architecture.

Examples:

X64  
ARM64  

---

### 9. Matrix Context — Variation Metadata

Matrix context reflects active configuration.

Example:

echo "Node version: ${{ matrix.node-version }}"

---

### 10. Matrix Properties Explained

#### matrix.os

Current OS variation.

#### matrix.node-version

Current Node variation.

#### matrix.extra

Custom variable from include.

---

### 11. Critical Gotcha: Missing Matrix Variables

Only included jobs have:

matrix.extra

Others → Undefined.

Safe usage:

${{ matrix.extra || 'none' }}

---

### 12. Env Context — Environment Variables

env context accesses variables defined via:

env:

Example:

${{ env.GLOBAL_ENV_VAR }}

---

### 13. Why Env Context Exists

Allows:

✔ Centralized configuration  
✔ Reuse across steps  
✔ Dynamic assignment  

---

### 14. Inputs Context — Manual Trigger Data

inputs context works with:

workflow_dispatch inputs.

Example:

${{ inputs.environment }}

---

### 15. Important Inputs Gotcha

Inputs:

✔ Only exist for workflow_dispatch  
✔ Undefined for push/schedule  

---

### 16. Secrets Context — Secure Values

Secrets accessed via:

${{ secrets.SECRET_NAME }}

Important rule:

Never print secrets directly.

---

### 17. Steps Context — Step Outputs

steps context accesses outputs.

Example:

${{ steps.step_output.outputs.commit_hash }}

---

### 18. How Step Outputs Work

Step writes:

echo "key=value" >> $GITHUB_OUTPUT

Later steps read via:

steps context.

---

### 19. Job Context — Job Metadata

Example:

${{ job.status }}

Values:

success  
failure  
cancelled  

Useful for conditional logic.

---

### 20. Strategy Context — Matrix Execution Metadata

Example:

${{ strategy.job-index }}

Represents:

Position of job in matrix.

Useful for:

✔ Selective execution  
✔ Debugging  

---

### 21. Context Interaction Mental Model

GitHub injects multiple layers:

github → Workflow metadata  
runner → Machine metadata  
matrix → Variation metadata  
env → Config metadata  
inputs → User metadata  
steps → Output metadata  
job → Execution metadata  
strategy → Matrix metadata  

Contexts are orthogonal information sources.

---

### 22. Why Contexts Are Critical

Contexts enable:

✔ Dynamic workflows  
✔ Conditional execution  
✔ Smart logging  
✔ Multi-environment automation  
✔ Matrix-aware logic  

Without contexts:

Automation would be rigid and repetitive.

---

### 23. Common Beginner Mistakes

Mistake 1:

Confusing shell variables with expressions.

Mistake 2:

Using contexts outside valid scope.

Mistake 3:

Printing secrets accidentally.

Mistake 4:

Wrong context property names.

Mistake 5:

Expecting contexts to persist between jobs.

---

### 24. Final Mental Model

Context = Runtime data source

Expression = Mechanism to read context

Contexts transform workflows from:

Static scripts

Into:

Intelligent, metadata-driven automation systems.

---

### 25. Closing Insight

Mastering contexts is the turning point in GitHub Actions learning.

Beginners write workflows.

Advanced users design **dynamic automation systems**.

Contexts are the foundation of that power.
