## Scheduling Workflow in GitHub Action

### 1. Introduction: Why Scheduled Workflows Exist

Not all automation should depend on developer actions.

Many tasks must run automatically based on time:

- Nightly builds
- Database backups
- Health checks
- Cleanup scripts
- Security scans
- Report generation

This is where **scheduled workflows** become essential.

Instead of:

Event-driven automation (push, PR)

We use:

Time-driven automation (cron schedule)

---

### 2. What is a Scheduled Workflow?

A scheduled workflow is:

A workflow triggered at predefined times.

GitHub Actions supports scheduling via:

schedule + cron expression

Mental model:

Clock → Workflow → Jobs → Steps

---

### 3. Big Picture Mental Model

Normal workflow:

push → run automation

Scheduled workflow:

Time matches cron → run automation

No human interaction required.

---

### 4. Basic Scheduled Workflow Example

	name: Nightly Workflow
	
	on:
		schedule:
			- cron: '0 0 * * *'
	
	jobs:
		steps_job:
			runs-on: ubuntu-latest
			steps:
				- name: Run Task
					run: echo "Running nightly task"

---

### 5. Understanding the `schedule` Event

schedule is a special trigger.

Unlike push or pull_request:

✔ Based on time  
✔ Uses cron syntax  
✔ Runs automatically  

---

### 6. What is a Cron Expression?

Cron = Time scheduling format.

Structure:

* * * * *

Each position represents:

Minute  
Hour  
Day of Month  
Month  
Day of Week  

---

### 7. Cron Field Breakdown

cron: '0 0 * * *'

Meaning:

0 → Minute  
0 → Hour  
* → Every day of month  
* → Every month  
* → Every weekday  

Translation:

Run at 00:00 (midnight UTC) every day.

---

### 8. Critical Gotcha: Timezone Confusion

GitHub cron uses:

UTC timezone

Not your local time.

Example:

cron: '0 0 * * *'

Runs at:

05:30 AM IST

Very common beginner confusion.

---

### 9. Example Cron Scenarios

Run every day at midnight UTC:

cron: '0 0 * * *'

---

Run every day at 6 AM UTC:

cron: '0 6 * * *'

---

Run every hour:

cron: '0 * * * *'

---

Run every 15 minutes:

cron: '*/15 * * * *'

---

Run every Sunday:

cron: '0 0 * * 0'

---

### 10. Special Cron Characters

	* → Any value  
	*/n → Every n intervals  
	, → Multiple values  
	- → Range  

Example:

cron: '0 9 * * 1-5'

Meaning:

Run at 9 AM UTC, Monday to Friday.

---

### 11. Multiple Scheduled Triggers

Workflows can have multiple schedules.

Example:

	on:
		schedule:
			- cron: '0 0 * * *'
			- cron: '0 12 * * *'

Runs twice daily.

---

### 12. Combining Schedule with Other Events

Very common pattern.

Example:
	
	on:
		push:
		schedule:
			- cron: '0 0 * * *'

Meaning:

Run on push AND nightly.

---

### 13. Runner Behavior in Scheduled Workflows

Identical to normal workflows.

✔ Fresh machine  
✔ Clean environment  
✔ No persistence  

Schedule does NOT change execution environment.

---

### 14. Common Use Cases

✔ Nightly builds  
✔ Cleanup jobs  
✔ Backup tasks  
✔ Monitoring checks  
✔ Dependency updates  
✔ Data sync  

---

### 15. Example: Cleanup Workflow

	name: Cleanup Workflow
	
	on:
		schedule:
			- cron: '0 2 * * *'
	
	jobs:
		cleanup:
			runs-on: ubuntu-latest
			steps:
				- name: Cleanup Logs
					run: echo "Cleaning old logs"

---

### 16. Critical Gotcha: Workflow Must Exist on Default Branch

Scheduled workflows only run if:

✔ Workflow file is on default branch (usually main)

Workflow only on feature branch → Schedule ignored.

Very common beginner mistake.

---

### 17. Why This Restriction Exists

Prevents:

✔ Accidental automation  
✔ Rogue branches triggering jobs  
✔ Unstable schedules  

---

### 18. Debugging Scheduled Workflows

If workflow doesn't run:

Check:

✔ Correct cron syntax  
✔ Workflow on default branch  
✔ Actions enabled  
✔ Repository not disabled  

---

### 19. Cron Syntax Errors

Invalid cron → Workflow ignored.

Example mistake:

cron: 'invalid expression' ❌

Always use valid format.

---

### 20. Testing Cron Expressions

Common strategy:

Temporarily use frequent schedule.

Example:

cron: '*/5 * * * *'

Runs every 5 minutes.

Useful for debugging.

---

### 21. Important Limitation: No Exact Precision Guarantee

GitHub states:

Scheduled workflows may be delayed.

Reasons:

✔ Load balancing  
✔ Queueing  
✔ Platform scheduling  

Cron ≠ Real-time guarantee.

---

### 22. Conditional Execution with Schedule

Workflows can detect trigger type.

Example:

if: github.event_name == 'schedule'

Useful for:

✔ Different logic per trigger  

---

### 23. Example Mixed Logic

	steps:
		- name: Run Only on Schedule
			if: github.event_name == 'schedule'
			run: echo "Scheduled execution"

---

### 24. Security Considerations

Scheduled workflows:

✔ Run automatically  
✔ No human supervision  

Be careful with:

✔ Destructive operations  
✔ Production changes  
✔ Expensive tasks  

---

### 25. Cost Considerations

GitHub-hosted runners:

✔ Billed usage time  

Frequent cron jobs → Higher usage.

Schedule responsibly.

---

### 26. Common Beginner Mistakes

Mistake 1:

Timezone misunderstanding.

Mistake 2:

Workflow not on default branch.

Mistake 3:

Invalid cron syntax.

Mistake 4:

Expecting exact execution timing.

Mistake 5:

Over-scheduling workflows.

---

### 27. Final Mental Model

schedule = Time-based trigger

cron = Timing specification

Workflow execution:

Identical to normal workflows.

Clock becomes your event source.

---

### 28. Closing Insight

Scheduled workflows transform GitHub Actions into:

Not just CI/CD engine…

But a **general automation scheduler**.

This unlocks:

✔ Maintenance tasks  
✔ Monitoring systems  
✔ Background automation  
✔ DevOps workflows  

Mastering cron = Essential automation skill.
