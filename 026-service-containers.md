## Service Containers

### 1. Introduction: The Limitation of Basic CI Testing

Most beginner workflows test things like:

✔ Linting  
✔ Unit tests  
✔ Build steps  

But real applications often depend on external services:

- Databases
- Caches
- Message queues
- APIs

Example:

Application uses PostgreSQL.

Unit tests alone are insufficient.

Why?

Because:

✔ Database queries must be tested  
✔ Schema interactions validated  
✔ Connection logic verified  

This is where **service containers** become essential.

---

### 2. The Core Problem Services Solve

GitHub runners are:

Fresh machines.

Meaning:

✔ No database running  
✔ No Redis running  
✔ No Kafka running  

Your application expects these dependencies.

Services simulate real infrastructure.

---

### 3. What is a Service Container?

Service container = Temporary containerized dependency.

Mental model:

Workflow job → Starts container → App connects → Tests run

Service containers provide:

✔ Databases  
✔ Supporting systems  
✔ Realistic test environments  

---

### 4. Big Picture Mental Model

Runner Machine  
	→ Your Steps  
	→ Service Containers  

Services behave like background infrastructure.

---

### 5. Why Containers Are Used

Containers provide:

✔ Isolation  
✔ Reproducibility  
✔ Fast startup  
✔ Clean teardown  

Perfect for CI environments.

---

## HANDS-ON EXAMPLE — Integration Testing with PostgreSQL

---

### 6. Scenario Setup

We simulate:

✔ Node.js application  
✔ PostgreSQL database  
✔ Integration tests  

Goal:

Workflow spins up Postgres → Tests run against it.

---

### 7. Workflow Example

    name: Integration Test Workflow
    
    on: push
    
    jobs:
    	test:
    		runs-on: ubuntu-latest
    
    		services:
    			postgres:
    				image: postgres:15
    				env:
    					POSTGRES_USER: test_user
    					POSTGRES_PASSWORD: test_password
    					POSTGRES_DB: test_db
    				ports:
    					- 5432:5432
    				options: >-
    					--health-cmd="pg_isready"
    					--health-interval=10s
    					--health-timeout=5s
    					--health-retries=5
    
    		steps:
    			- name: Checkout Repo
    			  uses: actions/checkout@v4
    
    			- name: Setup Node
    			  uses: actions/setup-node@v4
    			  with:
    				node-version: 18
    
    			- name: Install Dependencies
    			  run: npm install
    
    			- name: Run Tests
    			  env:
    				DB_HOST: localhost
    				DB_PORT: 5432
    				DB_USER: test_user
    				DB_PASSWORD: test_password
    				DB_NAME: test_db
    			  run: npm test

---

### 8. Understanding the `services` Section

services:

Defines containers started alongside job.

Each service:

✔ Runs automatically  
✔ Available during job  
✔ Destroyed after job  

---

### 9. Service Definition Breakdown

postgres → Service name (internal identifier)

Used for:

✔ Networking  
✔ Logs  
✔ Reference  

---

### 10. `image` Field

image: postgres:15

Specifies container image.

Pulled from Docker registry.

Equivalent to:

docker run postgres:15

---

### 11. Environment Variables for Container

    env:
    	POSTGRES_USER: test_user
    	POSTGRES_PASSWORD: test_password
    	POSTGRES_DB: test_db

Configures database instance.

Critical concept:

Containers require initialization configuration.

---

### 12. Ports Mapping

    ports:
    	- 5432:5432

Meaning:

Container Port → Runner Port

Application connects via:

localhost:5432

---

### 13. Networking Mental Model

Runner ↔ Service Container

GitHub creates internal Docker network.

Services accessible via:

✔ localhost (mapped ports)
✔ Service name (advanced networking)

---

### 14. Why Port Mapping Matters

Without ports:

Application cannot connect.

Common beginner failure:

Connection refused errors.

---

### 15. Health Checks — Critical Stability Mechanism

options:
	--health-cmd="pg_isready"

Health checks ensure:

✔ Service ready before steps run  
✔ Prevent race conditions  

Without health checks:

Tests may run before DB starts.

---

### 16. Race Condition Problem

Workflow starts → DB still booting → Tests fail

Health checks solve timing instability.

---

### 17. Health Check Parameters Explained

health-cmd → Readiness command  
health-interval → Check frequency  
health-timeout → Failure threshold  
health-retries → Retry attempts  

---

### 18. Steps Section (Normal Workflow Behavior)

Steps execute AFTER services start.

Important mental model:

Services initialized before steps.

---

### 19. Passing DB Config to Application

    env:
    	DB_HOST: localhost
    	DB_PORT: 5432
    	...

Application reads environment variables.

Simulates real runtime configuration.

---

### 20. Why Environment Variables Used

✔ Avoid hardcoding  
✔ Match production patterns  
✔ Dynamic configuration  

---

## ADVANCED CONCEPTS

---

### 21. Multi-Service Orchestration Example

    services:
    	postgres:
    		image: postgres:15
    
    	redis:
    		image: redis:7
    		ports:
    			- 6379:6379

Runner now has:

✔ Database
✔ Cache

---

### 22. Networking Between Services

Containers share network.

Services communicate internally.

Useful for:

✔ Microservice simulation  
✔ Complex integration testing  

---

### 23. Service Name Networking

Instead of localhost:

Use service name.

Example:

DB_HOST: postgres

Useful in container-native workflows.

---

### 24. Performance Considerations

Services introduce:

✔ Startup time  
✔ Resource overhead  

Tradeoff:

✔ Realistic testing  
✘ Slightly slower pipelines  

---

### 25. Common Beginner Mistakes

Mistake 1:

Forgetting ports mapping.

Mistake 2:

Ignoring health checks.

Mistake 3:

Wrong credentials mismatch.

Mistake 4:

Expecting services across jobs.

Mistake 5:

Confusing localhost vs service name.

---

### 26. Critical Gotcha: Services Are Job-Scoped

Services live ONLY within job.

Next job → Fresh runner → No services.

---

### 27. Why Services Are Essential for Real CI/CD

Enable:

✔ Integration tests  
✔ Database validation  
✔ Infrastructure simulation  
✔ System-level correctness  

Unit tests ≠ Full confidence.

---

### 28. Final Mental Model

Runner = Execution machine  
Services = Temporary infrastructure  
Containers = Reproducible dependencies  

Workflow becomes:

Automation + Infrastructure Simulation

---

### 29. Closing Insight

Basic CI tests logic.

Service containers test systems.

This marks the transition from:

Beginner automation → Real-world CI/CD engineering

Because production systems are never isolated.

Your CI environment should not be either.
