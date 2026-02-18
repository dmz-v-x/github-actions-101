## Matrix Strategy in GitHub Actions

### 1. Introduction: The Problem Matrix Strategy Solves

Consider a typical testing requirement:

✔ Test on multiple operating systems  
✔ Test on multiple Node.js versions  

Without matrix strategy:

You would need:

- Separate jobs for Linux
- Separate jobs for Windows
- Separate jobs for macOS
- Separate jobs for each Node version

Result:

✔ Massive duplication  
✔ Hard maintenance  
✔ Error-prone workflows  

Matrix strategy eliminates this repetition.

---

### 2. What is Matrix Strategy?

Matrix strategy allows:

Running a job multiple times with different configurations.

Mental model:

Define variations → GitHub generates jobs automatically

Instead of writing 9 jobs manually:

Matrix creates them dynamically.

---

### 3. Big Picture Mental Model

Matrix = Job generator

You define combinations → GitHub executes each combination.

---

### 4. The Workflow We Are Analyzing

    name: Node.js CI
    
    on:
    	push:
    		branches:
    			- main
    	pull_request:
    		branches:
    			- main
    
    jobs:
    	build:
    		runs-on: ${{ matrix.os }}
    
    		strategy:
    			matrix:
    				os: [ubuntu-latest, windows-latest, macos-latest]
    				node-version: [12, 14, 16]
    
    		steps:
    			- name: Checkout Code
    				uses: actions/checkout@v4
    
    			- name: Set up Node.js
    				uses: actions/setup-node@v2
    				with:
    					node-version: ${{ matrix.node-version }}
    
    			- name: Install Dependencies
    				run: npm Install
    
    			- name: Run Tests
    				run: npm tests

---

### 5. Understanding the Matrix Section

strategy:
	matrix:

This is where variations are defined.

Key idea:

Matrix defines dimensions of execution.

---

### 6. Matrix Dimensions Explained

os → Operating system variations  
node-version → Node.js variations  

GitHub computes:

All possible combinations.

---

### 7. How Many Jobs Will This Create?

Matrix values:

3 OS × 3 Node versions = 9 jobs

GitHub automatically generates:

✔ 9 independent executions  
✔ Running in parallel  

---

### 8. Visualization of Generated Jobs

GitHub internally creates:

ubuntu + node 12  
ubuntu + node 14  
ubuntu + node 16  

windows + node 12  
windows + node 14  
windows + node 16  

macos + node 12  
macos + node 14  
macos + node 16  

No duplication required.

---

### 9. `runs-on: ${{ matrix.os }}` — Dynamic Runner Selection

Instead of hardcoding:

runs-on: ubuntu-latest

Matrix injects OS value dynamically.

Example expansion:

runs-on: ubuntu-latest  
runs-on: windows-latest  
runs-on: macos-latest  

---

### 10. Accessing Matrix Variables

Matrix values are accessed via:

${{ matrix.VARIABLE_NAME }}

Example:

${{ matrix.os }}  
${{ matrix.node-version }}

---

### 11. Setup Node Step — Dynamic Configuration

node-version: ${{ matrix.node-version }}

Each generated job receives its own Node version.

This is the core power of matrix strategy.

---

### 12. Critical Gotcha: YAML Hyphen Names

Variable name:

node-version

Access syntax:

matrix.node-version ✔

Incorrect:

matrix.nodeVersion ❌

Names must match exactly.

---

### 13. Step Execution Behavior

Each matrix job:

✔ Runs independently  
✔ Has isolated runner  
✔ Separate logs  
✔ Separate failures  

---

### 14. Critical Gotcha: Jobs Are Still Isolated

Even with matrix:

✔ No shared filesystem  
✔ No shared dependencies  
✔ No shared environment  

Each job behaves like a normal job.

---

### 15. Install Dependencies Step (Contains Errors)

run: npm Install ❌  
run: npm tests ❌  

Correct commands:

run: npm install ✔  
run: npm test ✔  

Why this matters:

Commands are case-sensitive.

Very common beginner mistake.

---

### 16. Matrix Failure Behavior

If one matrix job fails:

✔ That job fails  
✔ Others continue  

Workflow result:

Fails if ANY required job fails.

---

### 17. Why Matrix is Crucial for CI

Ensures compatibility:

✔ Multiple OS support  
✔ Multiple runtime versions  
✔ Early detection of environment issues  

---

### 18. Real-World Use Cases

✔ Cross-platform testing  
✔ Multiple language versions  
✔ Browser compatibility testing  
✔ Infrastructure variations  

---

### 19. Advanced Matrix Example
    
    matrix:
    	os: [ubuntu-latest, windows-latest]
    	node-version: [16, 18]
    	environment: [staging, production]

Expands exponentially.

---

### 20. Controlling Matrix Explosion

Too many combinations → Slow pipelines.

Strategies:

✔ Limit dimensions  
✔ Exclude combinations  
✔ Use targeted testing  

---

### 21. Excluding Specific Combinations

    matrix:
    	os: [ubuntu-latest, windows-latest]
    	node-version: [12, 16]
    
    exclude:
    	- os: windows-latest
    	  node-version: 12

Prevents invalid combinations.

---

### 22. Including Custom Configurations

include:
	- os: ubuntu-latest
	  node-version: 18
	  experimental: true

Adds specific variations.

---

### 23. Performance Implications

Matrix increases:

✔ Parallelism  
✔ Compute usage  
✔ Speed of feedback  

But also increases:

✔ Resource consumption  

Balance is important.

---

### 24. Common Beginner Mistakes

Mistake 1:

Wrong matrix variable names.

Mistake 2:

Expecting sequential execution.

Mistake 3:

Case-sensitive command errors.

Mistake 4:

Matrix explosion (too many jobs).

Mistake 5:

Misunderstanding failure behavior.

---

### 25. Final Corrected Workflow

    name: Node.js CI
    
    on:
    	push:
    		branches:
    			- main
    	pull_request:
    		branches:
    			- main
    
    jobs:
    	build:
    		runs-on: ${{ matrix.os }}
    
    		strategy:
    			matrix:
    				os: [ubuntu-latest, windows-latest, macos-latest]
    				node-version: [12, 14, 16]
    
    		steps:
    			- name: Checkout Code
    				uses: actions/checkout@v4
    
    			- name: Set up Node.js
    				uses: actions/setup-node@v2
    				with:
    					node-version: ${{ matrix.node-version }}
    
    			- name: Install Dependencies
    				run: npm install
    
    			- name: Run Tests
    				run: npm test

---

### 26. Final Mental Model

Matrix strategy = Job multiplier

Define variations → GitHub generates combinations

Matrix variables:

✔ Dynamic runner selection  
✔ Dynamic configuration  
✔ Dynamic execution  

Without matrix:

Duplication-heavy workflows.

With matrix:

Scalable, elegant CI pipelines.

---

### 27. Closing Insight

Matrix strategy is one of the most powerful features in GitHub Actions.

It transforms workflows from:

Static pipelines

Into:

Dynamic execution systems capable of large-scale compatibility testing.
