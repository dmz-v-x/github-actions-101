## Creating first workflow 

### 1. Introduction: What We Are Building

In this guide, we are going to create:

Your **first GitHub Actions workflow**

But instead of blindly copying YAML, we will understand:

- Where the file lives
- Why it must live there
- How GitHub detects it
- What each line means
- Common beginner mistakes
- Why errors happen
- How to fix them

Goal:

Move from confusion → clarity.

---

### 2. What is a Workflow File?

A workflow file is simply a **YAML configuration file** that tells GitHub:

"When X happens → Run Y tasks"

Important truth:

GitHub Actions does **nothing** unless a workflow file exists.

No workflow file → No automation.

---

### 3. Where is a Workflow File Created?

This is a critical rule.

Workflow files **must** live inside:

.github/workflows/

Exact structure:

    Repository Root  
     └── .github  
          └── workflows  
               └── your_workflow.yml

Example:

.github/workflows/my-first-workflow.yml

If the file is placed anywhere else:

- GitHub will ignore it
- Workflow will never run
- No visible errors

This is a very common beginner mistake.

---

### 4. Why This Specific Folder?

GitHub scans repositories for workflow files only in:

.github/workflows/

This is a **convention-based system**.

GitHub does not search the entire repo.

Wrong location = Invisible workflow.

---

### 5. Creating Your First Workflow File (Step-by-Step)

Step 1: Open your repository

Step 2: Create folders:

.github/workflows/

Step 3: Create file:

my-first-workflow.yml

Step 4: Commit & push

Important:

Workflows only run after being pushed to GitHub.

Local file = No execution.

---

### 6. Our Workflow File (The Example)

We start with:

name: My First Workflow

on: push

    jobs:
      first_job:
        runs-on: ubuntu-lates
        steps:
          - name: Welcome message
            run: echo "My first GitHub Actions Job"

          - name: List files
            run: ls
    
          - name: Read file
            run: cat README.md

---

### 7. Understanding YAML Basics (Very Important)

YAML is indentation-sensitive.

Rules:

- Spaces matter
- Indentation defines structure
- Wrong spacing → Workflow failure

Example mistake:

jobs:
first_job:

Correct:

jobs:
  first_job:

Gotcha:

Use spaces, NOT tabs.

---

### 8. `name` — Workflow Identifier

name: My First Workflow

Purpose:

- Human-readable label
- Appears in GitHub UI
- Helps distinguish workflows

Gotcha:

Changing name does NOT affect logic.

Purely cosmetic.

---

### 9. `on` — Trigger Definition

on: push

Meaning:

Run workflow when a push event occurs.

push = Code pushed to repository.

Expanded version:

on:
  push:
    branches: [main]

Difference:

on: push → Any branch  
branches filter → Specific branch

Gotcha:

If workflow doesn’t run, check:

- Did event occur?
- Does branch match filter?

---

### 10. `jobs` — Container of Execution Units

jobs:

A workflow contains one or more jobs.

Key idea:

Workflow = Container  
Jobs = Execution units

---

### 11. `first_job` — Job Name

first_job:

This is an internal identifier.

Used for:

- Dependencies
- Debugging
- Logs

Gotcha:

Name is NOT displayed prominently in UI.

---

### 12. `runs-on` — Runner Selection

runs-on: ubuntu-lates

This line contains an error.

Correct value:

runs-on: ubuntu-latest

Meaning:

Use GitHub-hosted Ubuntu machine.

Common options:

- ubuntu-latest
- windows-latest
- macos-latest

Gotcha #1:

Typos break workflows.

ubuntu-lates → Invalid runner → Failure.

Gotcha #2:

Runner OS affects commands.

ls → Works on Linux/macOS  
dir → Works on Windows

---

### 13. `steps` — Sequential Instructions

steps:

A job contains steps executed in order.

Important rule:

Steps share the same environment.

---

### 14. Step Structure

Each step contains:

- name (optional but recommended)
- run OR uses

Example:

- name: Welcome message
  run: echo "Hello"

---

### 15. `run` — Execute Shell Commands

run: echo "My first GitHub Actions Job"

This executes a shell command.

Default shell:

Linux → bash  
Windows → PowerShell

Gotcha:

Commands must match OS.

---

### 16. Listing Files Step

run: ls

Meaning:

List files in current directory.

Key question:

What directory?

Answer:

Runner’s workspace.

---

### 17. Reading README Step

run: cat README.md

Meaning:

Print contents of README.md.

But this produces an error.

Why?

---

### 18. The Critical Beginner Gotcha — Repository Checkout

By default:

GitHub Actions runner does **NOT** contain your repository files.

Very important concept:

Runner starts as a clean machine.

No code. No files.

So:

ls → Empty directory  
cat README.md → File not found

This explains the error.

---

### 19. Why GitHub Doesn’t Auto-Checkout Code

Because workflows may:

- Not require repository files
- Perform external tasks
- Trigger APIs only

Checkout is explicit.

---

### 20. Fixing the Error — Adding Checkout Step

Correct workflow:

steps:
  - name: Checkout repository
    uses: actions/checkout@v4

  - name: Welcome message
    run: echo "My first GitHub Actions Job"

  - name: List files
    run: ls

  - name: Read file
    run: cat README.md

---

### 21. What `actions/checkout` Actually Does

This action:

- Clones repository
- Sets up workspace
- Makes files available

Without it:

No repository access.

---

### 22. Versioning Actions (`@v4`)

uses: actions/checkout@v4

Meaning:

Use version 4.

Why versions matter:

- Stability
- Reproducibility
- Avoid breaking changes

Gotcha:

Never omit version in production workflows.

---

### 23. What Else Do We Need in GitHub?

Minimal requirements:

✔ Repository  
✔ Workflow file in correct location  
✔ Commit & push  
✔ Trigger event  

Optional but common:

✔ Secrets (for credentials)  
✔ Branch filters  
✔ Permissions config  

---

### 24. How to See Workflow Execution

After push:

GitHub → Actions tab → Workflow runs

You can inspect:

- Logs
- Step outputs
- Failures

Gotcha:

If nothing appears:

Check file location.

---

### 25. Common Beginner Mistakes

Wrong folder:

.github/workflow/ ❌  
.github/workflows/ ✔

Typos:

ubuntu-lates ❌  
ubuntu-latest ✔

Indentation errors:

Misaligned YAML → Failure

Forgetting checkout:

File operations fail

Wrong OS commands:

Linux vs Windows mismatch

---

### 26. Final Mental Model

Workflow File:

Defines automation blueprint.

Trigger (`on`):

Defines start condition.

Jobs:

Execution units.

Runner (`runs-on`):

Execution machine.

Steps:

Sequential instructions.

Actions (`uses`):

Reusable logic.

Checkout:

Makes repository files available.

---

### 27. Closing Insight

Most GitHub Actions errors are not "complex CI problems".

They are:

- Path mistakes
- Indentation mistakes
- Typos
- Missing checkout

Once you understand the components:

Workflows become mechanical, not magical.

That is the turning point from beginner → confident user.
