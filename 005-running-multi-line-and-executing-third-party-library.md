## Running Multi-line commands & Executing third-party library

### 1. Introduction: What Are We Learning Here?

In this guide, we focus on two extremely important concepts:

1) Running **multi-line commands**
2) Executing **third-party libraries / tools**

These are foundational for real-world CI/CD pipelines.

Without mastering these:

- Workflows remain trivial
- Complex automation becomes difficult
- Errors become confusing

This workflow is ideal because it contains realistic beginner mistakes.

---

### 2. The Workflow We Are Analyzing
```
name: My First Workflow

on: push

jobs:
  first_job:
    runs-on: ubuntu-lates
    steps:

      - name: Checkout Repo
        uses: actions/checkout@v4

      - name: Executing Multiple Commnds
        run: |
          echo "My First Github Action Job"
          ls -ltr
          cat README.md

      - name: Executing Third party Library
        run: #Executing third pary libary command.

      - name: List files
        run: ls

      - name: Read file
        run: cat README.md
```
---

### 3. Big Picture Execution Flow

push → Workflow triggered → Job starts → Runner created → Steps execute

Key observation:

Everything runs on a **fresh machine**.

No persistence between runs.

---

### 4. `runs-on` — Contains a Typo (Very Important)

runs-on: ubuntu-lates ❌

Correct:

runs-on: ubuntu-latest ✔

Impact of typo:

- Runner cannot be allocated
- Job fails instantly
- No steps execute

Lesson:

Runner labels must be exact.

---

### 5. Why Runner Choice Matters

Runner determines:

- OS type
- Available tools
- Command compatibility
- Pre-installed software

ubuntu-latest = Linux environment

Commands like:

ls ✔  
cat ✔  
echo ✔  

---

### 6. Checkout Step — Required Foundation

- name: Checkout Repo
  uses: actions/checkout@v4

Why it is essential:

Multi-line commands operate on repository files.

Without checkout:

README.md does not exist.

---

### 7. Multi-Line Commands — The `run: |` Syntax

This line introduces a key YAML feature:

run: |

Meaning:

Execute **multiple shell commands**.

---

### 8. Why `|` is Needed

Without pipe syntax:

run: echo "Hello"
run: ls  ❌ Invalid YAML

With pipe:

run: |
  echo "Hello"
  ls ✔

Pipe tells YAML:

"Treat following lines as a single command block"

---

### 9. Mental Model of Multi-Line Execution

This:

run: |
  command1
  command2
  command3

Becomes:

Single shell session executing sequentially.

Equivalent to writing:

command1 && command2 && command3

Except:

Cleaner and more readable.

---

### 10. Command 1 — Echo

echo "My First Github Action Job"

Purpose:

- Logging
- Debugging
- Workflow visibility

Common real-world usage:

Print variables:

echo $GITHUB_REF
echo $GITHUB_SHA

---

### 11. Command 2 — `ls -ltr`

ls -ltr

Breakdown:

ls = List files  
-l = Long format  
-t = Sort by time  
-r = Reverse order  

Purpose:

- Inspect workspace
- Debug file presence
- Verify builds

---

### 12. Command 3 — `cat README.md`

cat README.md

Purpose:

- Verify checkout worked
- Debug file contents
- Inspect generated files

Gotcha:

Fails if checkout missing.

---

### 13. Multi-Line Command Failure Behavior

Critical rule:

If ANY command fails → Step fails.

Example:

echo ✔  
ls ✔  
cat missing.txt ❌ → Entire step fails

Implication:

Order of commands matters.

---

### 14. Debugging Strategy Using Multi-Line Blocks

Very common technique:

run: |
  pwd
  ls
  env

Helps diagnose:

- Wrong directories
- Missing files
- Missing variables

---

### 15. Executing Third-Party Library Step

- name: Executing Third party Library
  run: #Executing third pary libary command.

This step is intentionally incomplete.

But highlights a critical real-world issue.

---

### 16. The Core Problem with Third-Party Libraries

GitHub-hosted runners are:

✔ Fresh machines  
✔ Clean environments  
✔ Limited pre-installed tools  

Therefore:

Third-party libraries may NOT exist.

Example failures:

npm ❌ (if Node not installed)  
python ❌ (if Python missing)  
aws ❌ (if AWS CLI missing)

---

### 17. Why Pipelines Fail Here

Error cause:

Tool not installed on runner.

This is one of the **most common CI/CD errors**.

Symptoms:

command not found  
executable not found  
tool missing  

---

### 18. Solution Pattern — Install Dependencies Explicitly

Never assume tools exist.

Install them.

Example for Node:

- name: Setup Node
  uses: actions/setup-node@v4
  with:
    node-version: 20

---

### 19. Example: Installing a Third-Party Tool

Suppose we need:

jq (JSON processor)

Solution:

- name: Install jq
  run: sudo apt-get update && sudo apt-get install -y jq

Then:

- name: Use jq
  run: jq --version

---

### 20. Example: Using npm Library

Steps:

Setup Node → Install dependencies → Execute command

- uses: actions/setup-node@v4

- run: npm install

- run: npm test

---

### 21. Alternative Pattern — Use Prebuilt Actions

Instead of manual install:

Use marketplace actions.

Example:

uses: aws-actions/configure-aws-credentials@v4

Benefit:

✔ Less boilerplate  
✔ Fewer mistakes  
✔ Standardized setup  

---

### 22. Key Gotcha — Pipeline Environment is Ephemeral

Each workflow run:

✔ Starts clean  
✔ No memory of previous installs  

Therefore:

Always install dependencies per run.

---

### 23. Why Multi-Line Commands Help with Installations

Installations often require multiple commands:

run: |
  sudo apt-get update
  sudo apt-get install tool
  tool --version

Cleaner than chaining with &&.

---

### 24. Corrected Workflow (Conceptually Improved)
```
name: My First Workflow

on: push

jobs:
  first_job:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Repo
        uses: actions/checkout@v4

      - name: Executing Multiple Commands
        run: |
          echo "My First Github Action Job"
          ls -ltr
          cat README.md

      - name: Install Third Party Tool
        run: |
          sudo apt-get update
          sudo apt-get install -y jq

      - name: Execute Third Party Tool
        run: jq --version
```
---

### 25. Core Mental Models to Internalize

Multi-line commands (`run: |`):

✔ Execute sequential commands  
✔ Share same shell session  
✔ Fail on first error  

Third-party tools:

✔ Not guaranteed to exist  
✔ Must be installed explicitly  
✔ Environment resets every run  

Runner:

✔ Clean machine  
✔ No persistence  
✔ Predictable but empty  

---

### 26. Common Beginner Mistakes

Typos in runner labels

Assuming tools exist

Forgetting dependency installation

Ignoring command failure behavior

Misusing multi-line indentation

---

### 27. Closing Insight

Real GitHub Actions workflows are mostly about:

✔ Environment preparation  
✔ Dependency management  
✔ Sequential command execution  
