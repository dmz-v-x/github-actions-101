## Uploading & Downloading Artifact

### 1. Introduction: The Problem Artifacts Solve

One of the most confusing aspects of GitHub Actions:

Jobs are isolated.

This means:

- Each job runs on a separate machine
- Files do NOT automatically carry over
- Data produced in one job disappears

Real-world pipelines often require:

✔ Build files → Used by deploy  
✔ Test reports → Used by analysis  
✔ Generated assets → Used later  

Artifacts solve this exact problem.

---

### 2. What is an Artifact?

Artifact = Files saved from a job for later use.

Think of artifacts as:

Temporary storage attached to a workflow run.

They allow:

- Persisting files
- Sharing data between jobs
- Debugging outputs
- Download'sable results

---

### 3. Critical Mental Model

Without artifacts:

Job A filesystem ≠ Job B filesystem

With artifacts:

Job A → Upload files → GitHub storage → Job B downloads

Artifacts act as a **bridge between isolated jobs**.

---

### 4. Big Picture Workflow Flow

build job → generate files → upload artifact  
deploy job → download artifact → use files

---

### 5. Complete Example Workflow
```
name: Artifact Demo Workflow

on: push

jobs:

	build:
		runs-on: ubuntu-latest
		steps:

			- name: Checkout Repo
				uses: actions/checkout@v4

			- name: Generate File
				run: |
					echo "Hello from build job" > output.txt
					ls

			- name: Upload Artifact
				uses: actions/upload-artifact@v4
				with:
					name: build-output
					path: output.txt

	deploy:
		needs: build
		runs-on: ubuntu-latest
		steps:

			- name: Download Artifact
				uses: actions/download-artifact@v4
				with:
					name: build-output

			- name: Verify File
				run: |
					ls
					cat output.txt
```
---

### 6. Understanding the Build Job

The build job performs three major tasks:

✔ Checkout repository  
✔ Generate data  
✔ Upload artifact  

---

### 7. File Generation Step

run: |
	echo "Hello from build job" > output.txt

This creates:

output.txt

Key idea:

Artifacts store real files, not variables.

---

### 8. Why File Creation Matters

Artifacts work with:

✔ Files  
✔ Directories  
✔ Reports  
✔ Build outputs  

They do NOT store:

✔ Environment variables  
✔ Memory state  

---

### 9. Upload Artifact Step — The Core Component

uses: actions/upload-artifact@v4

This action sends files to GitHub storage.

---

### 10. Breaking Down Upload Configuration

with:
	name: build-output
	path: output.txt

#### name

Artifact identifier.

Used later for download.

Think of it as:

Storage label.

---

#### path

Specifies what files to store.

Can be:

✔ Single file  
✔ Multiple files  
✔ Entire directory  

Example:

path: dist/

---

### 11. Critical Gotcha — Wrong Paths

Very common error:

path: wrong-file.txt ❌

Result:

Upload step fails.

Lesson:

Files must exist before upload.

---

### 12. Artifact Storage Behavior

When uploaded:

✔ Files stored by GitHub  
✔ Available across jobs  
✔ Attached to workflow run  

Artifacts are NOT permanent.

They expire after retention period.

---

### 13. Deploy Job — Dependent Execution

deploy:
	needs: build

Ensures:

Artifact exists before download.

---

### 14. Download Artifact Step

uses: actions/download-artifact@v4

This retrieves stored files.

---

### 15. Breaking Down Download Configuration

with:
	name: build-output

Must match upload name EXACTLY.

Mismatch → Failure.

---

### 16. Where Files Are Downloaded

By default:

Downloaded into workspace directory.

After download:

output.txt exists locally.

---

### 17. Verifying Artifact Data

run: |
	ls
	cat output.txt

Purpose:

✔ Confirm download worked  
✔ Debug artifact content  

---

### 18. Core Mental Model of Artifact Flow

Step 1:

Job produces files.

Step 2:

Files uploaded to GitHub storage.

Step 3:

Next job downloads files.

Step 4:

Files used normally.

Artifacts behave like:

Temporary cloud filesystem.

---

### 19. Why Artifacts Are Essential in CI/CD

Real-world usage:

✔ Build binaries → Deploy  
✔ Coverage reports → Analysis  
✔ Logs → Debugging  
✔ Test results → Reporting  

Without artifacts:

Multi-job workflows break easily.

---

### 20. Artifact vs Cache (Important Distinction)

Artifacts:

✔ Share data between jobs  
✔ Persist outputs  
✔ Used for workflow communication  

Cache:

✔ Speed optimization  
✔ Dependency reuse  

Artifacts ≠ Performance tool

---

### 21. Common Beginner Mistakes

Mistake 1:

Forgetting upload step.

Mistake 2:

Wrong artifact name.

Mistake 3:

Wrong file path.

Mistake 4:

Expecting automatic file sharing.

Mistake 5:

Confusing artifacts with environment variables.

---

### 22. Uploading Multiple Files Example

path: |
	dist/
	reports/

Stores entire directories.

---

### 23. Downloading to Specific Directory

with:
	name: build-output
	path: downloaded-files/

Useful for structured pipelines.

---

### 24. Failure Behavior

If artifact missing:

Download step fails.

If dependency missing:

Job skipped.

---

### 25. Performance Considerations

Artifacts involve:

✔ Network transfer  
✔ Storage overhead  

Large artifacts → Slower workflows.

Upload only necessary files.

---

### 26. Security Considerations

Artifacts may contain:

✔ Build outputs  
✔ Logs  
✔ Reports  

Avoid uploading:

✔ Secrets  
✔ Credentials  
✔ Sensitive data  

Artifacts are accessible via workflow UI.

---

### 27. Final Mental Model

Jobs are isolated machines.

Artifacts are the bridge.

Upload:

Save files.

Download:

Restore files.

Without artifacts:

Cross-job data does not exist.

---

### 28. Closing Insight

Understanding artifacts unlocks real workflow design.

From this point onward, workflows become:

Not just execution pipelines…

But **data pipelines between jobs**.
