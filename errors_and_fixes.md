Got it — this is a **classic Docker agent startup failure**, and the timing you pasted actually tells us a lot.

```
python_agent (python:3.12-slim)
Started → died after ~7 seconds
java.io.IOException: Connection reset by peer
```

This **does NOT mean your pipeline is wrong**.
It means **the container exited before Jenkins could attach to it**.

---

## What’s really happening (plain English)

Jenkins is trying to do this:

1. Start a container from `python:3.12-slim`
2. Attach to it
3. Copy the Jenkins agent JAR
4. Run the agent Java process

But…

👉 **`python:3.12-slim` exits immediately**
Because:

* It has **no long-running process**
* No Java agent running
* No default command that “waits”

So Docker says: *container is gone*
Jenkins says: *connection reset by peer*

---

## 🔴 Root cause (important)

You are using:

```
Connect method: Attach Docker container
```

This method **requires**:

* Java installed in the image
* A command that keeps the container alive

`python:3.12-slim` ❌ does **not** meet those requirements.

---

## ✅ FIX OPTION 1 (Recommended): Use “Docker Agent” style (no Java needed)

### Change **Connect Method**

In your **Docker Agent Template**:

```
Connect method:
☑ Connect with JNLP (Docker Agent)
```

NOT:

```
Attach Docker container
```

Why this works:

* Jenkins injects the agent automatically
* No Java needed in the image
* Perfect for language images (Python, Node, Go)

### Required fields

| Field          | Value              |
| -------------- | ------------------ |
| Docker Image   | `python:3.12-slim` |
| Labels         | `python`           |
| Remote FS Root | `/home/jenkins`    |

Save → Run pipeline again ✅

---

## ✅ FIX OPTION 2: Keep “Attach Docker container” (advanced)

Only do this if you **know why you want it**.

You must:

1. Install Java in the image
2. Keep container alive

### Example custom Dockerfile

```dockerfile
FROM python:3.12-slim

RUN apt-get update && \
    apt-get install -y openjdk-17-jre && \
    rm -rf /var/lib/apt/lists/*

CMD ["sleep", "infinity"]
```

Then use **your custom image** in Jenkins.

⚠️ This is **overkill** for most pipelines.

---

## 🔍 How to confirm the issue yourself

Run locally:

```bash
docker run --rm python:3.12-slim
```

Result:

* Container starts
* Immediately exits

That’s exactly what Jenkins is experiencing.

---

## ✅ Best Practice (what pros do)

| Use case                       | Connect method          |
| ------------------------------ | ----------------------- |
| Python / Node / Go / Terraform | **JNLP (Docker Agent)** |
| Custom CI images with Java     | Attach Docker container |
| Jenkins inbound agents         | `jenkins/inbound-agent` |

---

## TL;DR

* ❌ `python:3.12-slim` + **Attach Docker container** = crash
* ✅ Use **Connect with JNLP**
* Jenkins error is expected, not your fault
* Container died before Jenkins could attach

If you want, next we can:

* Convert your setup to **best-practice JNLP**
* Or build a **proper custom Python CI image**
* Or debug the container live with logs

Just tell me 👍
