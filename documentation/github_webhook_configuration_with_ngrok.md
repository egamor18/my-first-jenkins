

---

## 1️⃣ Your ngrok tunnel

```
Forwarding    https://actiniform-briley-remillable.ngrok-free.dev -> http://localhost:8080
```

This means:

* **External URL (publicly reachable)**:
  `https://actiniform-briley-remillable.ngrok-free.dev`

* **Internal target (your Jenkins)**:
  `http://localhost:8080`

Everything hitting that ngrok URL will **forward traffic straight to your local Jenkins**.
No port forwarding, no DS-Lite headache.

---

## 2️⃣ Quick test

From any external machine (or GitHub itself):

```bash
curl -I https://actiniform-briley-remillable.ngrok-free.dev
```

Expected output:

* `HTTP/1.1 200 OK` → perfect
* Or `HTTP/1.1 403 Forbidden` → also fine (Jenkins auth)

This proves Jenkins is now **publicly reachable**.

---

## 3️⃣ Configure GitHub webhook

1. Go to your repo → **Settings → Webhooks → Add webhook**
2. **Payload URL**:

```
https://actiniform-briley-remillable.ngrok-free.dev/github-webhook/
```

3. **Content type**: `application/json`
4. Optional: set a **Secret**
5. Events: “Just the push event”
6. Save

---

## 4️⃣ Enable trigger in Jenkins

In your job:

* Check **GitHub hook trigger for GITScm polling**
* Save

---

## 5️⃣ Test the end-to-end flow

1. Make a small commit:

```bash
git commit --allow-empty -m "Test ngrok webhook"
git push
```

2. Watch Jenkins → the job should trigger immediately

If yes → you’re fully online! 🎯

---

## ⚠️ Important note

* Free ngrok URLs **change every time you restart the tunnel**
* If ngrok stops/restarts, **update the webhook URL** in GitHub
* For persistent URLs, consider **paid ngrok** or **Cloudflare Tunnel**

---

