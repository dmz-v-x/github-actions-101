## Executing Shell Scripts

### 1. Introduction: Why Shell Scripts Matter in Workflows

As workflows grow, writing long inline commands becomes messy.

Instead of this:

run: |
  command1
  command2
  command3
  command4
  command5

We often move logic into **shell scripts**.

Why?

- Cleaner workflows
- Reusable automation logic
- Easier debugging
- Better maintainability
- Separation of concerns

Understanding shell script execution is a core CI/CD skill.

---

### 2. What Does "Executing a Shell Script" Mean?

A shell script is simply:

A file containing commands executed by a shell.

Example file:

script.sh

Contents:

	echo "Hello"
	ls
	cat README.md

In GitHub Actions:

We tell the runner to execute this file.

---

### 3. Typical Workflow Using Shell Scripts

Example structure:
```
name: My First Workflow

on: push

jobs:
	first_job:
		runs-on: ubuntu-latest
		steps:

			- name: Checkout Repo
				uses: actions/checkout@v4

			- name: Run Shell Script
				run: ./script.sh
```
---

### 4. Big Picture Execution Flow

push → Workflow triggered → Runner created → Repo checked out → Script executed

Key insight:

Scripts live inside your repository.

Checkout becomes mandatory.

---

### 5. Why Checkout is Required for Scripts

Without checkout:

Runner workspace = Empty

Script file = Missing

Error:

./script.sh: No such file or directory

Lesson:

Scripts are just files → Files require checkout.

---

### 6. `run: ./script.sh` — What Actually Happens?

This line tells the runner:

"Execute the file named script.sh"

Equivalent to running in terminal:

./script.sh

Important:

Runner uses shell environment (bash on Linux).

---

### 7. Critical Gotcha — File Permissions

Very common beginner error:

./script.sh: Permission denied

Why?

Script is not executable.

---

### 8. Why This Error Happens

GitHub repository files may lack execute permission.

Runner respects Linux file permissions.

If script lacks execute bit → Failure.

---

### 9. Fixing Permission Errors

Solution:

Add permission step.

run: chmod +x script.sh

Updated workflow:

- name: Make Script Executable
	run: chmod +x script.sh

- name: Run Script
	run: ./script.sh

---

### 10. What `chmod +x` Actually Does

chmod = Change mode  
+x = Add execute permission  

Without it:

Script = Plain text file

With it:

Script = Executable program

---

### 11. Alternative Execution Method (Safer)

Instead of:

./script.sh

You can use:

bash script.sh

Example:

run: bash script.sh

Why safer?

Does not depend on execute permission.

---

### 12. Difference Between `./script.sh` vs `bash script.sh`

./script.sh:

✔ Requires execute permission  
✔ Uses script’s shebang  

bash script.sh:

✔ No execute permission needed  
✔ Explicit interpreter  

---

### 13. The Shebang Line (Important Concept)

Shell scripts often start with:

#!/bin/bash

This tells the OS:

Which interpreter to use.

Without shebang:

Runner may still execute via default shell, but behavior can vary.

---

### 14. Example Proper Script File

script.sh:

	#!/bin/bash

	echo "Starting workflow"
	ls
	cat README.md

Best practice:

Always include shebang.

---

### 15. Multi-Line Logic vs Script Files

Inline commands:

✔ Good for small tasks  
✔ Bad for complex logic  

Scripts:

✔ Good for reusable logic  
✔ Cleaner workflows  

---

### 16. Debugging Advantage of Scripts

Scripts allow:

- Local testing
- Version control
- Easier iteration

You can run:

bash script.sh

Before pushing.

---

### 17. Common Script Execution Errors

Error 1:

No such file or directory

Cause:

Missing checkout / wrong path.

---

Error 2:

Permission denied

Cause:

Missing execute permission.

---

Error 3:

Command not found

Cause:

Missing dependencies inside script.

---

### 18. Path Gotcha — Scripts in Subfolders

Example:

scripts/build.sh

Workflow:

run: bash scripts/build.sh

Incorrect path → Failure.

---

### 19. Runner Environment Gotcha

Scripts run inside:

Runner OS environment.

If script contains:

dir (Windows command)

Fails on:

ubuntu-latest.

Always match OS and commands.

---

### 20. Dependency Gotcha Inside Scripts

Scripts often use tools:

node  
python  
jq  
aws  

These tools may NOT exist.

Install before running script.

---

### 21. Example Realistic Workflow
```
name: Script Execution Workflow

on: push

jobs:
	first_job:
		runs-on: ubuntu-latest

		steps:
			- name: Checkout Repo
				uses: actions/checkout@v4

			- name: Setup Node
				uses: actions/setup-node@v4
				with:
					node-version: 20

			- name: Install Dependencies
				run: npm install

			- name: Run Script
				run: bash script.sh
```
---

### 22. Why This Pattern is Common

Prepare environment → Execute script

Because scripts assume dependencies exist.

---

### 23. Best Practices for Shell Scripts

✔ Always include shebang  
✔ Keep scripts idempotent  
✔ Avoid interactive commands  
✔ Log useful information  
✔ Fail fast on errors  

---

### 24. Fail Fast Principle

Bad script:

command1
command2
command3

Better script:

set -e

Stops execution on first failure.

---

### 25. Why `set -e` Matters

Without it:

Script continues after errors.

With it:

Script stops immediately.

Critical for CI reliability.

---

### 26. Final Mental Model

Shell script = Just a file

Runner = Clean machine

Checkout = Makes file available

chmod +x = Makes file executable

bash script.sh = Explicit execution

Scripts = Externalized workflow logic

---

### 27. Closing Insight

Executing shell scripts is not an advanced feature.

It is how real workflows stay manageable.

As pipelines grow:

Workflows become orchestrators.

Scripts become logic containers.

This separation is a hallmark of production-grade CI/CD systems.
