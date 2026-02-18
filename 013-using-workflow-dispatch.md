## Using `workflow-dispatch` in github action

### 1. Introduction: Why Manual Triggers Exist

Most workflows are triggered automatically:

- push
- pull_request
- schedule

But sometimes automation must run **on demand**.

Examples:

- Deploy only when button is clicked
- Run maintenance manually
- Trigger expensive jobs selectively
- Test pipelines without pushing commits

This is where **workflow_dispatch** becomes essential.

---

### 2. What is `workflow_dispatch`?

`workflow_dispatch` is a manual trigger event.

It allows users to:

✔ Click a button in GitHub UI  
✔ Start workflow manually  
✔ Provide custom inputs  

Mental model:

User → Click Run Workflow → Jobs Execute

---

### 3. Basic Workflow Dispatch Example

    name: Manual Workflow
    
    on:
    	workflow_dispatch:
    
    jobs:
    	manual_job:
    		runs-on: ubuntu-latest
    		steps:
    			- name: Manual Step
    				run: echo "Workflow triggered manually"

---

### 4. How This Differs from Other Triggers

push → Code change required  
schedule → Time required  
workflow_dispatch → Human action required

No commit needed.

No cron needed.

---

### 5. Where Do You Trigger It?

GitHub UI:

Repository → Actions Tab → Select Workflow → Run workflow

Critical requirement:

Workflow file must exist on default branch.

---

### 6. Critical Gotcha: Workflow Must Be on Default Branch

Very common beginner mistake:

Workflow only exists in feature branch.

Result:

Run workflow button missing.

Reason:

GitHub only enables manual triggers from default branch.

---

### 7. Why This Restriction Exists

Prevents:

✔ Accidental executions  
✔ Rogue branches triggering automation  
✔ Unstable manual runs  

---

### 8. Big Picture Execution Flow

Click Run → Workflow starts → Runner allocated → Jobs execute

Same execution model as other workflows.

Only trigger mechanism differs.

---

### 9. Adding Inputs to Manual Workflows

One of the most powerful features.

Allows dynamic runtime configuration.

---

### 10. Example Workflow with Inputs

    name: Deploy Workflow
    
    on:
    	workflow_dispatch:
    		inputs:
    			environment:
    				description: "Deployment Environment"
    				required: true
    				default: "staging"
    
    jobs:
    	deploy:
    		runs-on: ubuntu-latest
    		steps:
    			- name: Print Environment
    				run: echo "Deploying to ${{ github.event.inputs.environment }}"

---

### 11. Understanding Inputs Structure

    workflow_dispatch:
    	inputs:

Inputs allow users to provide values when clicking Run.

---

### 12. Input Field Breakdown

environment → Input name  
description → UI label  
required → Must provide value  
default → Pre-filled value  

---

### 13. Accessing Input Values

Syntax:

${{ github.event.inputs.INPUT_NAME }}

Example:

${{ github.event.inputs.environment }}

---

### 14. Critical Gotcha: Inputs Are Expressions, Not Shell Variables

Incorrect:

echo $environment ❌

Correct:

echo "${{ github.event.inputs.environment }}" ✔

OR assign to env:

env:
	ENV_NAME: ${{ github.event.inputs.environment }}

---

### 15. Assigning Inputs to Environment Variables

Very common pattern.

Example:

    - name: Deploy Step
    	env:
    		ENVIRONMENT: ${{ github.event.inputs.environment }}
    	run: echo "Deploying to $ENVIRONMENT"

---

### 16. Multiple Inputs Example

    workflow_dispatch:
    	inputs:
    		version:
    			description: "Version Number"
    			required: true
    		region:
    			description: "Deployment Region"
    			required: true

---

### 17. Real-World Use Cases

✔ Controlled deployments  
✔ Manual maintenance tasks  
✔ Debugging workflows  
✔ Re-running failed pipelines  
✔ Running expensive jobs selectively  

---

### 18. Combining with Other Triggers

Very common design.

Example:

    on:
    	push:
    	workflow_dispatch:

Meaning:

Auto + Manual triggers supported.

---

### 19. Detecting Trigger Type

Useful for conditional logic.

Example:

if: github.event_name == 'workflow_dispatch'

---

### 20. Example Conditional Logic

    steps:
    	- name: Manual Only Step
    		if: github.event_name == 'workflow_dispatch'
    		run: echo "Manual execution logic"

---

### 21. Permissions & Security Considerations

Manual workflows:

✔ Can trigger sensitive operations  
✔ Often used for deployments  

Best practices:

✔ Restrict branch access  
✔ Use environment protections  
✔ Use secrets securely  

---

### 22. Common Beginner Mistakes

Mistake 1:

Workflow not on default branch.

Mistake 2:

Wrong input syntax.

Mistake 3:

Expecting inputs as shell variables automatically.

Mistake 4:

Forgetting required inputs.

Mistake 5:

Confusing secrets with inputs.

---

### 23. Inputs vs Secrets

Inputs:

✔ Provided by user  
✔ Visible in UI  
✔ Non-sensitive config  

Secrets:

✔ Encrypted  
✔ Hidden  
✔ Sensitive data  

Never use inputs for passwords.

---

### 24. Debugging Manual Workflows

If button missing:

✔ Check default branch  
✔ Check YAML syntax  
✔ Check Actions enabled  

If inputs missing:

✔ Check indentation  
✔ Check inputs structure  

---

### 25. Final Mental Model

workflow_dispatch = Manual trigger

Inputs = Runtime configuration

Execution environment:

Same as any workflow.

Difference:

User becomes the event source.

---

### 26. Closing Insight

Manual triggers transform workflows into:

Not just reactive automation…

But **interactive automation systems**.

This is critical for:

✔ Safe deployments  
✔ Controlled operations  
✔ Professional CI/CD pipelines  
✔ Real DevOps workflows  

Mastering workflow_dispatch = Major GitHub Actions milestone.
