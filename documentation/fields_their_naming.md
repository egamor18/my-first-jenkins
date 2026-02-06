Great question — this page is *packed* with knobs, and Jenkins does a terrible job explaining **when you actually need them**.
I’ll go field-by-field in **plain English**, with **practical “when to use it / when to ignore it” guidance**.

This applies to a **Pipeline job** (Declarative or Scripted).

---

# 🧩 General

### **Description**

Free-text documentation for the job.

**Use it for**

* What this pipeline builds
* Repo / service name
* Deployment target (dev/stage/prod)

👉 *Purely informational. Doesn’t affect execution.*

---

### **Discard old builds**

Controls how many past builds Jenkins keeps.

**Why it matters**

* Jenkins disks fill up fast
* Old logs + artifacts eat space

**Typical settings**

* Keep last **10–30 builds**
* Keep builds for **14–30 days**

**Best practice**
✔ Always enable this in real pipelines.

---

### **Do not allow concurrent builds**

Prevents two builds of the same job from running at the same time.

**Enable this if**

* You deploy to a shared environment
* You use shared resources (DB, filesystem, Terraform state)
* Your pipeline is not concurrency-safe

**Disable if**

* Each build is fully isolated
* You want faster feedback for multiple commits

---

### **Do not allow the pipeline to resume if the controller restarts**

If Jenkins crashes/restarts, the pipeline will **fail instead of resuming**.

**Enable this if**

* Your steps are not resumable (SSH, manual scripts)
* You prefer a clean restart

**Disable (default) if**

* You want long pipelines to resume automatically

👉 Mostly useful in fragile or legacy setups.

---

### **GitHub project**

Adds a clickable GitHub URL on the job page.

**What it does NOT do**

* Does NOT connect webhooks
* Does NOT trigger builds

👉 UI-only convenience.

---

### **Pipeline speed / durability override**

Trade-off between:

* **Speed** (less disk writes)
* **Durability** (can resume after crash)

**Options**

* High durability → safer, slower
* Performance optimized → faster, riskier

**Best practice**

* Leave **default**
* Change only if you understand pipeline durability internals

---

### **Preserve stashes from completed builds**

Keeps `stash {}` data after build finishes.

**Enable if**

* You need artifacts from old builds
* You debug or replay pipelines

**Disable if**

* You don’t use stashes heavily

⚠️ Can increase disk usage significantly.

---

### **This project is parameterized**

Adds input parameters (dropdowns, strings, booleans).

**Common uses**

* Environment selection (dev/stage/prod)
* Feature flags
* Version numbers

**Example**

```groovy
parameters {
  choice(name: 'ENV', choices: ['dev', 'prod'])
}
```

✔ Extremely common in real pipelines.

---

### **Throttle builds**

Limits how many builds can run at once (globally or per node).

**Use this if**

* You have limited agents
* Builds are resource-heavy
* You want fairness across jobs

👉 Requires **Throttle Concurrent Builds Plugin**.

---

# ⏱ Triggers

---

### **Build after other projects are built**

Triggers this job when another job finishes.

**Old-school Jenkins**

* Tightly couples jobs
* Hard to reason about

🚫 Generally avoided in modern pipeline design
✔ Prefer pipelines triggering pipelines via API or Git events

---

### **Build periodically**

Cron-style scheduled builds.

**Example**

```text
H 2 * * *
```

(Run daily at a random minute after 2am)

**Use for**

* Nightly builds
* Dependency checks
* Scheduled scans

---

### **GitHub hook trigger for GITScm polling**

Build triggers when GitHub sends a webhook.

**This is what you want for CI**
✔ Fast
✔ Event-driven

**Requires**

* GitHub webhook configured
* Jenkins reachable from GitHub

👉 Recommended over polling.

---

### **Poll SCM**

Jenkins periodically checks Git for changes.

**Downsides**

* Slow
* Wastes resources
* Not real-time

🚫 Avoid if webhooks are possible
✔ Use only if GitHub can’t reach Jenkins

---

### **Trigger builds remotely**

Allows builds via HTTP call.

**Example**

```bash
curl http://jenkins/job/myjob/build?token=XYZ
```

**Use cases**

* Trigger from scripts
* Trigger from other systems

⚠️ Secure with tokens + permissions.

---

# 🧪 Pipeline

---

### **Definition**

How Jenkins gets the pipeline code.

**Pipeline script**

* Groovy written directly in Jenkins UI
* Harder to version and review

**Pipeline script from SCM**
✔ Best practice
✔ Jenkinsfile lives in Git repo

---

### **Script**

The actual Groovy pipeline code.

---

### **Try sample Pipeline**

Loads example Jenkinsfiles.

👉 Useful when learning.

---

### **Use Groovy Sandbox**

Restricts unsafe Groovy operations.

**Enable if**

* You don’t trust all authors
* Shared Jenkins instance

**Disable if**

* You need advanced Groovy features
* You control all pipeline code

---

### **Pipeline Syntax**

UI helper to generate Groovy snippets.

👉 Great for beginners.

---

# 🔧 Advanced (Pipeline)

### **Advanced (bottom section)**

Low-level options (timeouts, quiet period, retries).

**Most users**
✔ Leave defaults

---

# 🧠 Recommended minimal setup (real-world CI)

For a typical GitHub CI pipeline:

* ✔ Discard old builds
* ✔ Do not allow concurrent builds (if deploying)
* ✔ GitHub hook trigger
* ✔ Pipeline from SCM
* ✔ Parameterized (if env-based)
* ❌ Poll SCM
* ❌ Build after other projects

---
