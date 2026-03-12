# Chris Daily Finance — Content Generator
## Setup Guide

### What's in this folder
```
webapp/
├── index.html          ← The web app (single file, everything included)
├── n8n-workflow.json   ← Import this into n8n
├── Dockerfile          ← For Coolify deployment
├── nginx.conf          ← Nginx config (used by Docker)
└── SETUP.md            ← This file
```

---

## Step 1 — Import the n8n Workflow

1. Open your n8n instance
2. Go to **Workflows** → **Import from File**
3. Select `n8n-workflow.json`
4. The workflow will appear with all 10 nodes pre-configured

### Configure the OpenRouter credential in n8n
Each of the 3 AI nodes (`Script Generator`, `Script Rewriter`, `Image Prompt Generator`) uses an HTTP Header Auth credential for OpenRouter.

1. Click any AI node → click the **Credential** dropdown
2. Create a new **Header Auth** credential:
   - **Name:** `Authorization`
   - **Value:** `Bearer YOUR_OPENROUTER_API_KEY`
3. Apply the same credential to all 3 AI nodes

### Get your Webhook URL
1. Click the **Webhook Trigger** node
2. Copy the **Production URL** (looks like: `https://your-n8n.com/webhook/chris-finance-generator`)
3. Keep this — you'll paste it into the web app

### Activate the workflow
- Toggle the workflow to **Active** (top right of the workflow editor)

---

## Step 2 — Deploy the Web App via Coolify

### Option A: Deploy from Git (Recommended)
1. Push this `webapp/` folder to a GitHub repo
2. In Coolify → **New Resource** → **Application** → **GitHub**
3. Select the repo and set **Build Pack** to `Dockerfile`
4. Set the **Root Directory** to `webapp/` if it's in a subfolder
5. Click **Deploy**

### Option B: Deploy directly via Dockerfile
1. In Coolify → **New Resource** → **Application** → **Docker Image** / **Dockerfile**
2. Upload the `Dockerfile` + `index.html` + `nginx.conf`
3. Set port to `80`
4. Deploy

---

## Step 3 — Connect Web App to n8n

1. Open your deployed web app URL
2. At the top, paste your n8n webhook URL into the **n8n Webhook URL** field
3. The URL is saved automatically in your browser (localStorage)

---

## Step 4 — Test It

### Topic Mode Test
1. Select **"Generate from Topic"**
2. Select **"Long-form"**
3. Type: `Roth IRA vs 401k`
4. Click **Generate**
5. Wait ~30–60 seconds for DeepSeek to process
6. Verify:
   - Script appears with [SCENE X] labels
   - Image Prompts tab has one card per scene
   - Each prompt mentions the skeleton character

### Script Mode Test
1. Select **"Rewrite a Script"**
2. Paste the Electrician vs Engineer script
3. Click **Generate**
4. Verify it rewrites in Two Paths format with random names + ages

---

## Notes

- **Response time:** ~30–90 seconds depending on video length (DeepSeek processes sequentially per scene)
- **Cost:** DeepSeek via OpenRouter is extremely cheap (~$0.01–0.05 per full generation)
- **CORS:** The n8n webhook is configured with `Access-Control-Allow-Origin: *` — if you lock down to a specific domain later, update the Respond to Webhook node headers
