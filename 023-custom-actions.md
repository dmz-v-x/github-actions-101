## Custom Actions

### 1. Introduction: When Composite Actions Are Not Enough

Composite actions are excellent for bundling steps.

But they have limitations.

Composite actions:

✔ Combine existing steps  
✔ Run shell commands  
✔ Reuse workflow logic  

They cannot easily handle:

✘ Complex logic  
✘ Advanced computations  
✘ External API orchestration  
✘ Custom runtime behavior  
✘ Heavy scripting  

When workflows require real programming logic, we use:

Custom Actions.

---

### 2. What is a Custom Action?

Custom Action = Fully programmable automation unit.

Instead of YAML step bundles, you write:

✔ JavaScript code  
✔ Docker containers  

Mental model:

Composite Action → Step wrapper  
Custom Action → Real executable program

---

### 3. Two Types of Custom Actions

GitHub supports:

1) JavaScript Actions  
2) Docker Actions  

Each solves different problems.

---

### 4. When to Use JavaScript Actions

Ideal when:

✔ Logic-heavy operations  
✔ Fast execution required  
✔ Node.js ecosystem needed  
✔ Lightweight automation  

Examples:

- Data transformation
- API orchestration
- Dynamic computation
- Smart decision engines

---

### 5. When to Use Docker Actions

Ideal when:

✔ Custom runtime required  
✔ Non-Node tooling needed  
✔ System-level dependencies  
✔ Complex environments  

Examples:

- Python tools
- CLI-heavy operations
- Custom binaries
- Multi-language logic

---

## PART 1 — JavaScript Custom Action (Hands-On)

---

### 6. JavaScript Action Mental Model

Structure:

Action Folder  
	→ action.yml (metadata)  
	→ index.js (logic)

---

### 7. Creating Action Folder

Inside repository:

.github/actions/my-js-action/

---

### 8. action.yml — The Metadata File

Every custom action requires:

action.yml

Example:

    name: "My JS Action"
    description: "Example JavaScript Action"
    
    inputs:
    	name:
    		description: "Name to greet"
    		required: true
    
    outputs:
    	message:
    		description: "Greeting message"
    
    runs:
    	using: "node20"
    	main: "index.js"

---

### 9. Understanding Metadata Fields

name → Action identifier  
description → Documentation  
inputs → Parameters  
outputs → Returned values  
runs → Execution configuration  

Critical rule:

Metadata drives execution behavior.

---

### 10. Writing Action Logic (index.js)

index.js:

	const core = require('@actions/core')

	try {
		const name = core.getInput('name')

		const message = `Hello ${name}, from custom JS action`

		core.setOutput('message', message)

		console.log(message)

	} catch (error) {
		core.setFailed(error.message)
	}

---

### 11. Key JavaScript APIs

core.getInput() → Read inputs  
core.setOutput() → Set outputs  
core.setFailed() → Mark failure  

---

### 12. Calling JS Action from Workflow

Workflow:
    
    jobs:
    	demo:
    		runs-on: ubuntu-latest
    		steps:
    
    			- name: Run Custom JS Action
    			  id: greet
    			  uses: ./.github/actions/my-js-action
    			  with:
    				name: "Himanshu"
    
    			- name: Print Output
    			  run: echo "${{ steps.greet.outputs.message }}"

---

### 13. Execution Flow

Workflow → Calls action → JS executes → Output returned

---

## PART 2 — Docker Custom Action (Hands-On)

---

### 14. Docker Action Mental Model

Structure:

Action Folder  
	→ action.yml  
	→ Dockerfile  
	→ Entry script

---

### 15. action.yml for Docker Action

    name: "My Docker Action"
    description: "Example Docker-based Action"
    
    inputs:
    	name:
    		required: true
    
    runs:
    	using: "docker"
    	image: "Dockerfile"

---

### 16. Dockerfile Example

Dockerfile:

	FROM ubuntu:latest

	RUN apt-get update && apt-get install -y curl

	COPY entrypoint.sh /entrypoint.sh

	RUN chmod +x /entrypoint.sh

	ENTRYPOINT ["/entrypoint.sh"]

---

### 17. Entrypoint Script

entrypoint.sh:

	#!/bin/bash

	echo "Hello $1 from Docker Action"

---

### 18. Passing Inputs into Docker

Docker actions receive inputs as arguments.

Updated script:

entrypoint.sh:

	#!/bin/bash

	echo "Hello $INPUT_NAME"

---

### 19. Calling Docker Action from Workflow

    - name: Run Docker Action
      uses: ./.github/actions/my-docker-action
      with:
    	name: "Himanshu"

---

### 20. JS vs Docker Actions — Key Differences

JavaScript Action:

✔ Faster startup  
✔ Lightweight  
✔ Ideal for logic  

Docker Action:

✔ Flexible runtime  
✔ Heavy dependencies  
✔ Slower startup  

---

### 21. Performance Considerations

JS Actions:

✔ Near-instant execution  
✔ No container overhead  

Docker Actions:

✘ Container build/startup cost  
✘ Slower cold start  

Choose wisely.

---

### 22. Publishing Custom Actions

Actions can be:

✔ Private (internal reuse)  
✔ Public (GitHub Marketplace)

Publishing steps:

1) Create dedicated repository  
2) Add action.yml  
3) Tag version (v1, v2)  
4) Reference via:

uses: owner/repo@v1

---

### 23. Why Versioning is Critical

Without versioning:

Updates break consumers.

With versioning:

Stable automation contracts.

---

### 24. Reusability Strategy

Custom actions shine when:

✔ Logic reused frequently  
✔ Complex automation needed  
✔ Organization tooling created  
✔ Standard workflows enforced  

---

### 25. Composite vs Custom Actions

Composite Action:

✔ YAML-based  
✔ Simple reuse  

Custom Action:

✔ Code-based  
✔ Complex logic  

Composite → Abstraction  
Custom → Execution engine

---

### 26. Common Beginner Mistakes

Mistake 1:

Forgetting action.yml

Mistake 2:

Wrong metadata configuration

Mistake 3:

Incorrect input/output handling

Mistake 4:

Using Docker unnecessarily

Mistake 5:

Ignoring versioning

---

### 27. Final Mental Model

Composite Actions → Reuse steps  
Custom Actions → Build automation programs  

JS Actions → Logic efficiency  
Docker Actions → Runtime flexibility  

---

### 28. Closing Insight

Custom Actions represent a major leap:

From writing workflows…

To building automation tools.

This is where GitHub Actions evolves into:

✔ Platform engineering  
✔ Internal DevOps tooling  
✔ Automation architecture  

Mastering custom actions = Advanced CI/CD engineering milestone.
