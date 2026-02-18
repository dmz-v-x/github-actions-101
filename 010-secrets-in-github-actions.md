## Secrets in GitHub Actions

### 1. Introduction: Why Secrets Exist in GitHub Actions

In real workflows, we often need sensitive data:

- API keys
- Tokens
- Passwords
- Cloud credentials
- Database URLs

Hardcoding them inside workflow files is dangerous:

- Visible in repository
- Exposed to collaborators
- Leaked in logs
- Security risk

GitHub Secrets solve this exact problem.

Secrets = Secure, encrypted variables.

---

### 2. What is a Secret?

A secret is:

An encrypted value stored in GitHub, injected at runtime.

Secrets are:

✔ Encrypted at rest  
✔ Hidden in logs  
✔ Not stored in repo  
✔ Accessible via workflow  

Example types:

API_KEY  
DB_PASSWORD  
AWS_SECRET  
TOKEN  

---

### 3. Critical Mental Model

Workflow file = Public configuration  
Secrets = Private data vault  

Secrets never live in code.

---

### 4. Where Are Secrets Created?

Secrets are configured inside GitHub UI.

Repository → Settings → Secrets and variables → Actions → New repository secret

Steps:

1. Open repository
2. Go to Settings
3. Secrets and variables
4. Actions
5. New secret

---

### 5. Example Workflow Using Secrets (Different Example)

Let’s build a realistic example:

Calling a protected API.
```
name: Using Secrets Demo

on: push

env:
	API_BASE_URL: https://api.example.com

jobs:
	api_call:
		runs-on: ubuntu-latest

		steps:
			- name: Checkout Repo
				uses: actions/checkout@v4

			- name: Call Protected API
				env:
					API_TOKEN: ${{ secrets.API_TOKEN }}
				run: |
					echo "Calling API..."
					curl -H "Authorization: Bearer $API_TOKEN" \
						 $API_BASE_URL/data
```
---

### 6. Understanding Workflow-Level `env`

env:
	API_BASE_URL: https://api.example.com

Purpose:

Defines environment variables available across entire workflow.

Benefits:

✔ Avoid duplication  
✔ Centralized configuration  
✔ Cleaner jobs  

---

### 7. Jobs Section

jobs:
	api_call:

Defines a job named:

api_call

Job = Independent execution unit.

---

### 8. Runner Selection

runs-on: ubuntu-latest

Determines:

✔ OS  
✔ Shell  
✔ Available tools  

curl works on ubuntu-latest.

---

### 9. Checkout Step

uses: actions/checkout@v4

Required if:

✔ Using repo files  
✔ Running scripts  
✔ Accessing workspace  

---

### 10. Secrets Injection — The Key Line

API_TOKEN: ${{ secrets.API_TOKEN }}

This is the core concept.

Meaning:

Inject value stored in GitHub Secrets.

---

### 11. Important Syntax Breakdown

${{ secrets.API_TOKEN }}

Components:

${{ }} → Expression syntax  
secrets → Secrets context  
API_TOKEN → Secret name  

Secret names are case-sensitive.

---

### 12. Why Secrets Must Use Expression Syntax

Incorrect:

API_TOKEN: secrets.API_TOKEN ❌

Correct:

API_TOKEN: ${{ secrets.API_TOKEN }} ✔

---

### 13. Step-Level Environment Variables

env:
	API_TOKEN: ...

Scope:

Available ONLY inside this step.

Good practice for secrets.

Why?

Limits exposure.

---

### 14. Why Not Define Secrets at Workflow Level?

Technically possible, but risky.

Better:

Inject secrets at smallest scope needed.

Security principle:

Least privilege exposure.

---

### 15. Using Secret Inside Commands

curl -H "Authorization: Bearer $API_TOKEN"

Secrets behave like environment variables.

Accessed via:

$VARIABLE_NAME

---

### 16. Log Masking Behavior

GitHub automatically masks secrets.

If printed accidentally:

echo $API_TOKEN

Logs show:

***

Not actual value.

---

### 17. Critical Gotcha — Secrets Not Available Locally

Secrets only exist:

✔ During workflow execution  
✔ Inside GitHub runner  

Running workflow locally → Secrets missing.

---

### 18. Common Beginner Errors

Error 1:

Secret not found

Cause:

Secret not created in GitHub.

---

Error 2:

Empty variable

Cause:

Wrong secret name / typo.

---

Error 3:

Authentication failure

Cause:

Invalid secret value.

---

### 19. Secret Naming Rules

Best practices:

✔ Uppercase  
✔ Clear naming  
✔ No spaces  

Examples:

API_TOKEN ✔  
api_token ❌ (bad style)  
My Secret ❌  

---

### 20. Secrets vs Environment Variables

Environment Variables:

✔ Stored in workflow  
✔ Visible in repo  
✔ Non-sensitive config  

Secrets:

✔ Stored in GitHub  
✔ Encrypted  
✔ Sensitive data  

---

### 21. Secrets Scope Types

GitHub supports:

Repository secrets  
Environment secrets  
Organization secrets  

Scope determines accessibility.

---

### 22. Job-Level Environment Variables

Example:

    jobs:
    	build:
    		env:
    			NODE_ENV: production

Available across job steps.

---

### 23. Step-Level Environment Variables

Example:

    - name: Step
    	env:
    		DEBUG: true

Available only in step.

---

### 24. Workflow-Level Environment Variables

Example:

env:
	GLOBAL_CONFIG: value

Available everywhere.

---

### 25. Combining Secrets with Env Variables

Very common pattern:

    env:
    	API_URL: https://api.example.com
    
    steps:
    	env:
    		API_KEY: ${{ secrets.API_KEY }}

---

### 26. Security Best Practices

✔ Never hardcode secrets  
✔ Use least scope exposure  
✔ Rotate secrets periodically  
✔ Avoid echoing secrets  
✔ Validate secret names  

---

### 27. Debugging Secrets Safely

Bad debugging:

echo $API_TOKEN ❌

Better debugging:

echo "Token exists: ${API_TOKEN:+YES}" ✔

---

### 28. Final Mental Model

Workflow = Blueprint  
Environment Variables = Configuration  
Secrets = Secure injected values  

Secrets:

✔ Encrypted  
✔ Runtime-only  
✔ Log-masked  

Correct usage:

${{ secrets.SECRET_NAME }}

---

### 29. Closing Insight

Most CI/CD security mistakes happen because of:

Hardcoded credentials.

Secrets are not optional.

They are foundational for:

✔ Secure automation  
✔ Production workflows  
✔ Professional pipelines  

Mastering secrets = Essential GitHub Actions skill.
