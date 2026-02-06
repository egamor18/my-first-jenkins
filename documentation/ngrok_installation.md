
---

## Step 1: Install ngrok on Ubuntu 24.04

### Option A: Snap (simplest)

```bash
sudo snap install ngrok
```

---

### Option B: Official binary (if you prefer)

```bash
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc \
  | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null

echo "deb https://ngrok-agent.s3.amazonaws.com buster main" \
  | sudo tee /etc/apt/sources.list.d/ngrok.list

sudo apt update
sudo apt install ngrok
```

---

## Step 2: Authenticate ngrok

Create or log into an ngrok account:

* [https://dashboard.ngrok.com](https://dashboard.ngrok.com)

Copy your **Auth Token**, then run:

```bash
ngrok config add-authtoken <YOUR_TOKEN>
```

This links ngrok to your account.

---

## Step 3: Start Jenkins (confirm)

Make sure Jenkins is running:

```bash
sudo systemctl status jenkins
```

You want:

```
Active: active (running)
```

---

## Step 4: Start the tunnel

Run:

```bash
ngrok http 8080
```

You’ll see something like:

```
Forwarding  https://abc123.ngrok.io -> http://localhost:8080
```

That **https URL** is the magic door 🚪

---

## Step 5: Test with curl (confidence check)

From *any* external machine (or even the same one):

```bash
curl -I https://abc123.ngrok.io
```

Expected:

* `200 OK` or
* `403 Forbidden`

Either means Jenkins is reachable ✔️

---

## Step 6: Configure GitHub webhook

In your GitHub repo:

1. **Settings → Webhooks → Add webhook**
2. Payload URL:

   ```
   https://abc123.ngrok.io/github-webhook/
   ```
3. Content type:

   ```
   application/json
   ```
4. Secret:

   * optional but recommended
5. Events:

   * Just the push event
6. Save

---

## Step 7: Enable webhook trigger in Jenkins

In your Jenkins job:

* ✔ GitHub hook trigger for GITScm polling
* Save

---

## Step 8: Test the full flow

1. Push a commit to GitHub
2. Watch Jenkins
3. Build triggers 🎯

If it triggers, you’re officially wired end-to-end.

---

## Important ngrok notes (real talk)

* Free ngrok URLs **change on restart**
* That means:

  * Restart ngrok → update GitHub webhook URL
* For stable URLs:

  * Paid ngrok
  * Or Cloudflare Tunnel later

---

## Security minimum (please don’t skip)

* Jenkins auth enabled
* No anonymous builds
* Webhook secret configured

We can lock this down once it works.

---

----------------------




