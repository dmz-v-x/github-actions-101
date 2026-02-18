## Dependency Caching

### 1. Introduction: The Hidden Performance Bottleneck in CI/CD

One of the biggest inefficiencies in workflows:

Repeated dependency installation.

Example:

push → npm install  
push again → npm install  
push again → npm install  

Even if dependencies did NOT change.

Problems created:

✔ Slow pipelines  
✔ Wasted compute minutes  
✔ Poor developer feedback speed  

Dependency caching exists to eliminate redundant work.

---

### 2. The Core Problem Caching Solves

Workflows run on:

Fresh machines.

Meaning:

✔ No previous state  
✔ No saved dependencies  
✔ Everything reinstalled  

Caching reintroduces reuse into an otherwise stateless system.

---

### 3. Cache vs Artifact — Critical Foundational Distinction

This confusion is extremely common.

Artifacts:

✔ Share files between jobs  
✔ Persist outputs  
✔ Data transfer mechanism  

Cache:

✔ Speed optimization  
✔ Dependency reuse  
✔ Performance mechanism  

Mental model:

Artifacts → Communication  
Cache → Acceleration

---

### 4. Artifact Mental Model

Build job → Upload artifact → Deploy job downloads

Focus:

Data sharing.

---

### 5. Cache Mental Model

Workflow run → Restore dependencies → Skip reinstall

Focus:

Avoid recomputation.

---

### 6. What is Dependency Caching?

Caching stores reusable files between workflow runs.

Examples:

✔ Node modules  
✔ Python packages  
✔ Maven dependencies  
✔ Build tool caches  

Cache = Persistent storage layer across runs.

---

### 7. Introducing `actions/cache`

GitHub provides official caching action:

actions/cache

Purpose:

✔ Save files  
✔ Restore files  
✔ Accelerate workflows  

---

### 8. Basic Caching Example (Node.js)

    name: Cache Demo
    
    on: push
    
    jobs:
    	build:
    		runs-on: ubuntu-latest
    
    		steps:
    			- name: Checkout Repo
    			  uses: actions/checkout@v4
    
    			- name: Restore Cache
    			  uses: actions/cache@v4
    			  with:
    				path: node_modules
    				key: node-cache-${{ hashFiles('package-lock.json') }}
    
    			- name: Install Dependencies
    			  run: npm install

---

### 9. Understanding Cache Fields

path → What to cache  
key → Cache identity  

Cache behavior driven entirely by key design.

---

### 10. Why Cache Key is the Most Important Concept

Key determines:

✔ Cache reuse  
✔ Cache invalidation  
✔ Cache accuracy  

Poor key → Broken caching strategy.

---

### 11. Cache Key Design Explained

key: node-cache-${{ hashFiles('package-lock.json') }}

Meaning:

✔ Generate hash of lock file  
✔ Dependencies change → Hash changes  
✔ Cache invalidates automatically  

---

### 12. Why Hash Lock Files?

Lock files define:

Exact dependency graph.

Change lock file → Dependencies changed.

Perfect invalidation trigger.

---

### 13. Cache Hit vs Cache Miss

Cache Hit:

✔ Key found  
✔ Files restored  
✔ Install step faster  

Cache Miss:

✔ No matching key  
✔ Dependencies reinstalled  
✔ Cache updated later  

---

### 14. Cache Hit Mental Model

Restore → Dependencies exist → npm install faster

---

### 15. Cache Miss Mental Model

No restore → Fresh install → Cache rebuilt

---

### 16. Cache Invalidation — Extremely Important Concept

Cache invalidation = When cache becomes unusable.

Triggers:

✔ Dependency changes  
✔ Key changes  
✔ Explicit key updates  

---

### 17. Automatic Invalidation Strategy

Use dynamic keys:

${{ hashFiles(...) }}

Ensures cache updates when dependencies change.

---

### 18. Manual Invalidation Strategy

Update static prefix:

key: v2-node-cache-...

Forces fresh cache.

---

### 19. Restore Keys — Advanced Fallback Mechanism

Example:

restore-keys: |
	node-cache-

Meaning:

✔ Try exact key  
✔ If not found → Try partial match  

Useful for:

✔ Progressive cache reuse  
✔ Graceful degradation  

---

### 20. Example Restore Strategy

    uses: actions/cache@v4
    with:
    	path: node_modules
    	key: node-cache-${{ hashFiles('package-lock.json') }}
    	restore-keys: |
    		node-cache-

---

### 21. Performance Tradeoffs of Caching

Caching helps when:

✔ Install cost high  
✔ Dependencies stable  
✔ Large dependency graphs  

Caching hurts when:

✔ Cache upload/download heavy  
✔ Small dependency sets  
✔ Constant dependency churn  

Cache ≠ Always beneficial.

---

### 22. Cache Size Tradeoff

Large cache:

✔ Faster execution  
✘ Slower restore/save  

Small cache:

✔ Faster restore  
✘ Less benefit  

---

### 23. Real-World Speed Optimization Patterns

✔ Cache dependencies  
✔ Cache build tool outputs  
✔ Cache package managers  
✔ Cache language runtimes  

---

### 24. Example: Caching npm Cache Directory

path: ~/.npm

Instead of node_modules.

Benefits:

✔ Smaller cache  
✔ Faster restore  
✔ Still accelerates install  

---

### 25. Multi-Dimensional Cache Keys

Example:

key: node-${{ runner.os }}-${{ hashFiles('package-lock.json') }}

Separates caches per OS.

Critical because:

Linux ≠ Windows dependencies.

---

### 26. OS-Specific Cache Gotcha

Sharing cache across OS → Failures.

Always include:

${{ runner.os }}

When needed.

---

### 27. Cache Lifecycle Mental Model

Restore Cache → Run job → Save Cache

Cache updated only if:

✔ Key not found  
✔ Files changed  

---

### 28. Common Beginner Mistakes

Mistake 1:

Using static keys → Stale caches.

Mistake 2:

Caching wrong directories.

Mistake 3:

Oversized caches.

Mistake 4:

Confusing artifacts with cache.

Mistake 5:

Expecting cache sharing between jobs.

---

### 29. Cache vs Job Isolation Gotcha

Cache persists across runs.

Jobs remain isolated.

Cache restores per job independently.

---

### 30. Final Mental Model

Artifacts → Share data between jobs  
Cache → Reuse data between runs  

Cache key = Cache identity

Hash files = Invalidation trigger

Caching transforms workflows from:

Stateless execution

Into:

Stateful performance-optimized pipelines.

---

### 31. Closing Insight

Dependency caching is one of the highest-impact optimizations in CI/CD.

Done correctly:

✔ Massive speed gains  
✔ Reduced compute usage  
✔ Faster developer feedback  

Done incorrectly:

✔ Stale builds  
✔ Cache misses  
✔ Pipeline confusion  

Caching is not just a feature.

It is a **design discipline**.
