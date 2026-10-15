# How to Make Blockbench Models and Animate Them Using AI
### Connecting Blockbench → Antigravity via MCP (Video Notes)

---

## What this setup does

Normally an AI can only guess at animation numbers blind — it can't see the model. With **Blockbench MCP** (a community bridge plugin) connected to **Antigravity**, the AI agent gets a *live* connection to your open Blockbench project: it can create/edit models, add animation keyframes, and take screenshots of the actual viewport to check and correct its own work.

**Prerequisites:**
- Blockbench **desktop app** (v4.8+) — NOT the web version
- **Node.js 18+** installed
- **Antigravity** installed

---

## Step 1 — Install prerequisites

- Download Blockbench desktop: https://www.blockbench.net/downloads
- Download Node.js: https://nodejs.org (LTS version)
- Verify Node installed correctly:
  ```
  node -v
  npm -v
  ```

---

## Step 2 — Clone and build the MCP server

Open a terminal (PowerShell on Windows) and run:

```
git clone https://github.com/sosadly/blockbench-mcp.git
cd blockbench-mcp
npm install
npm run build
```

**Verify it worked:**
```
dir dist
```
You should see `index.js`, `client.js`, and `tools.js` inside the `dist` folder.

> **Troubleshooting:** If `npm run build` fails with `'tsc' is not recognized`, run `npm install --save-dev typescript` and try the build again. If that still fails, delete `node_modules` and `package-lock.json`, then run `npm install` fresh.

---

## Step 3 — Load the bridge plugin inside Blockbench

1. Open Blockbench.
2. Go to **File ▸ Plugins ▸ Load Plugin from File**.
3. Select `plugin/blockbench_mcp.js` from inside your cloned `blockbench-mcp` folder.
4. When it asks for network permission, choose **"Always allow for this plugin."**
5. You should see a toast notification: *"MCP server started on port 8787."*

**Verify the bridge is live** (open a terminal, keep Blockbench open):
```
curl http://127.0.0.1:8787/ping
```
Expected response:
```json
{"ok":true,"protocol":1,"blockbench_version":"5.2.1","is_app":true,"has_project":true}
```

---

## Step 4 — Connect it to Antigravity

1. Open **Antigravity**.
2. Click the **`...`** (Additional Options) menu in the Agent panel.
3. Click **MCP Servers ▸ Manage MCP Servers ▸ View raw config**.
4. This opens `mcp_config.json`. Add the `blockbench` entry:

```json
{
    "mcpServers": {
        "blockbench": {
            "command": "node",
            "args": [
                "C:\\Users\\YOUR_USERNAME\\blockbench-mcp\\dist\\index.js"
            ]
        }
    }
}
```

> Replace the path with your actual full path to `dist/index.js`. Use **double backslashes** (`\\`) on Windows since it's inside a JSON string.

5. **Save the file.**
6. Go back to **Manage MCP Servers** and click **Refresh**.
7. Confirm `blockbench` shows as **connected** with its tool list visible (modeling, animation, texturing, screenshot tools, etc.).

---

## Step 5 — Use it

With Blockbench open and a model loaded, just prompt the Antigravity agent directly, e.g.:

> "Open the current project, add an idle animation on the right arm that looks like it's waving to call the player over, and take a screenshot when you're done so I can check it."

Because the agent has a **screenshot tool**, it can see the actual pose in the viewport and adjust angles itself — instead of an AI (like a chat-only assistant) guessing keyframe numbers with no visual feedback.

---

## Notes for the video

- Emphasize the difference between chat-only AI (blind keyframe editing) vs. MCP-connected AI (can see + iterate via screenshots).
- Show the `curl` ping test on screen — good visual proof the bridge is alive before jumping into Antigravity.
- Show the raw `mcp_config.json` file being edited — viewers often get JSON syntax wrong (mismatched braces/commas), so pointing out valid vs. invalid JSON on screen is worth 30 seconds.
- Good demo: ask the agent to build a simple animation, screenshot it, then ask for a specific tweak ("raise the arm higher") to show the iterate-and-correct loop live.

---

## Security note (worth a quick mention on camera)

This plugin exposes an `execute_script` tool that can run arbitrary JavaScript inside Blockbench. Only load plugins/bridges from sources you trust — worth a one-line disclaimer in the video description.
