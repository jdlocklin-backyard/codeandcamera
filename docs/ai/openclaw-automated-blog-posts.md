---
title: "AI That Writes About What It Just Built"
description: "How I wired OpenClaw into my homelab so it can draft blog posts automatically about the work it does — this post included."
tags:
  - ai
  - automation
  - homelab
  - intermediate
published: false
author: "JD Locklin"
date_created: "2026-05-09"
date_updated: "2026-05-09"
difficulty: "Intermediate"
estimated_time: "1-2 hours"
---

# AI That Writes About What It Just Built

## Overview

I run OpenClaw as a persistent AI assistant on my homelab server. Today I gave it full access to my homelab — Proxmox, GitHub, Slack, YouTube, the filesystem — through a set of MCP servers it wired up itself. Then I asked it to write a blog post about what it just did.

This is that post.

!!! success "The Transformation"
    **BEFORE:** After finishing a homelab project, writing a post about it was a separate multi-hour effort — remembering what I did, reconstructing the steps, formatting it correctly.

    **AFTER:** The AI assistant that did the work drafts the post immediately. It already knows every step, every error, every decision. I review and publish.

---

| Detail | Information |
|--------|-------------|
| **Difficulty** | Intermediate |
| **Time Required** | 1-2 hours initial setup, minutes per post after |
| **Category** | AI & Machine Learning |
| **Last Updated** | May 2026 |

**Key Technologies:** OpenClaw, MCP, Docker, GitHub Actions, MkDocs

---

## What You'll Learn

- How to run OpenClaw as a persistent homelab AI assistant with full tool access
- How to build an MCP ecosystem (Docker gateway, GitHub, Proxmox, Slack, YouTube)
- How to use a homelab-sync repo to share configs across machines
- How to prompt an AI to draft blog posts from its own work history

---

## The Problem

Homelab projects have a documentation debt problem. You spend an evening wiring something up, hit three unexpected errors, figure them out, and end up with something working. Then you close the terminal. The troubleshooting steps, the specific errors, the why behind each decision — gone. If you write a post about it later, you're reconstructing from memory.

The irony is that AI assistants are perfect note-takers. They see every step. They know what broke and why. They made the decisions. Getting that information out of a session and into a publishable post should be trivial.

It wasn't — until now.

---

## How AI Helped

Everything in this setup was built in a single session with OpenClaw acting autonomously. The session covered:

- **Diagnosing and fixing** a loopback CLI scope upgrade issue that had been blocking `openclaw agent` invocations
- **Setting up the Docker MCP Gateway** with four servers (filesystem, fetch, time, sequentialthinking)
- **Wiring GitHub MCP** using the existing `gh` auth token
- **Cloning and configuring** ProxmoxMCP-Plus with dual-transport containers (REST on 8811, SSE on 8812)
- **Connecting Slack** (`joesbot` on Locklin Sandbox)
- **Adding YouTube transcript** support via `mcp-youtube-transcript`
- **Creating the homelab-sync repo** as a cross-machine config exchange point
- **Writing and pushing** the Proxmox API token blog post — including fixing a broken image reference that failed CI under `--strict` mode
- **Updating CLAUDE.MD** with guardrails to prevent that broken image issue from repeating
- **Writing this post**

The AI didn't just do what it was told. It diagnosed the scope issue independently, read the OpenClaw docs, found the right fix, and applied it. It found my Proxmox IP and token name from my own public blog post before I'd told it either. It pushed back when I asked it to store secrets in a git repo.

---

## Architecture

```mermaid
flowchart TB
    subgraph host["🖥️ openclaw-host (Linux)"]
        OC["OpenClaw Gateway<br/>port 18789"]
        DG["Docker MCP Gateway<br/>port 3100 SSE"]
        PM["ProxmoxMCP-Plus<br/>8811 REST / 8812 SSE"]
        YT["youtube-transcript<br/>stdio"]
    end

    subgraph mcp["☁️ Remote MCP"]
        GH["GitHub MCP<br/>api.githubcopilot.com"]
        SL["Slack MCP<br/>npx stdio"]
    end

    subgraph docker["🐳 Docker MCP Profile: openclaw"]
        FS["filesystem"]
        FE["fetch"]
        TI["time"]
        ST["sequentialthinking"]
    end

    subgraph prox["🖧 Proxmox VE (10.0.0.130)"]
        PVE["pve node<br/>11 VMs"]
    end

    OC --> DG
    OC --> PM
    OC --> YT
    OC --> GH
    OC --> SL
    DG --> docker
    PM --> prox
```

---

## Prerequisites

- [ ] OpenClaw installed and running as a systemd service
- [ ] Docker + Docker Compose installed
- [ ] GitHub CLI (`gh`) authenticated
- [ ] Proxmox VE instance with an API token
- [ ] Slack bot token with appropriate scopes
- [ ] A site to publish to (this one uses MkDocs + GitHub Pages)

---

## Step 1: Fix Loopback CLI Scope

If you run `openclaw agent` from a shell script or cron job, the CLI device needs more than `operator.read`. The symptom:

```
gateway connect failed: scope upgrade pending approval
gateway closed (1008): pairing required: device asking for more scopes than currently approved
```

The fix is to upgrade the CLI device's approved scopes. The device pairing records live at:

- **Server side:** `~/.openclaw/devices/paired.json`
- **Client side:** `~/.openclaw/identity/device-auth.json`

Find the CLI device (`clientId: "cli"`) in `paired.json` and update `scopes`, `approvedScopes`, and the token scopes to include `operator.admin`. Mirror the change in `device-auth.json`, clear `pending.json`, then restart the gateway.

```bash
openclaw gateway restart
openclaw devices list  # Verify CLI device shows operator.admin
openclaw agent --agent main --session-id test --message "ping"  # Should return: pong
```

!!! warning "Note"
    `commands.ownerAllowFrom` controls command ownership routing, not scope approval. Setting it to `["loopback:joe"]` alone does not fix this error.

---

## Step 2: Install the Docker MCP Gateway

Docker's MCP Gateway runs MCP servers as isolated containers and exposes them over SSE. No Docker Desktop required.

```bash
# Download binary (check releases for latest version)
curl -fsSL https://github.com/docker/mcp-gateway/releases/download/v0.42.1/docker-mcp-linux-amd64.tar.gz \
  -o /tmp/docker-mcp.tar.gz
tar -xzf /tmp/docker-mcp.tar.gz -C /tmp
mv /tmp/docker-mcp ~/.docker/cli-plugins/docker-mcp
chmod +x ~/.docker/cli-plugins/docker-mcp

# Required on non-Desktop Docker
export DOCKER_MCP_IN_CONTAINER=1
docker mcp feature enable profiles
docker mcp catalog pull mcp/docker-mcp-catalog
```

Create a profile with useful servers:

```bash
docker mcp profile create \
  --name "openclaw" \
  --server "catalog://mcp/docker-mcp-catalog/filesystem" \
  --server "catalog://mcp/docker-mcp-catalog/time" \
  --server "catalog://mcp/docker-mcp-catalog/fetch" \
  --server "catalog://mcp/docker-mcp-catalog/sequentialthinking"

# Configure filesystem paths
docker mcp profile config openclaw --set 'filesystem.paths=["/home/joe"]'
```

Run it as a systemd user service:

```ini title="~/.config/systemd/user/docker-mcp-gateway.service"
[Unit]
Description=Docker MCP Gateway
After=docker.service

[Service]
Type=simple
Environment=DOCKER_MCP_IN_CONTAINER=1
ExecStart=/home/joe/.docker/cli-plugins/docker-mcp gateway run \
  --profile openclaw \
  --transport sse \
  --port 3100 \
  --log-calls
Restart=on-failure

[Install]
WantedBy=default.target
```

```bash
systemctl --user daemon-reload
systemctl --user enable --now docker-mcp-gateway
```

Register in OpenClaw:

```bash
openclaw mcp set docker-mcp '{"url":"http://localhost:3100/sse","transport":"sse"}'
```

---

## Step 3: Wire Up Remote MCP Servers

**GitHub MCP** (uses existing `gh` auth):

```bash
GH_TOKEN=$(gh auth token)
openclaw mcp set github-mcp \
  "{\"url\":\"https://api.githubcopilot.com/mcp/\",\"transport\":\"streamable-http\",\"headers\":{\"Authorization\":\"Bearer ${GH_TOKEN}\"}}"
```

**YouTube Transcripts** (free, no API key):

```bash
pip install mcp-youtube-transcript --break-system-packages
openclaw mcp set youtube-transcript '{"command":"mcp-youtube-transcript","args":[]}'
```

**Slack:**

```bash
openclaw mcp set slack '{
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-slack"],
  "env": {
    "SLACK_BOT_TOKEN": "xoxb-YOUR-TOKEN",
    "SLACK_TEAM_ID": "YOUR-TEAM-ID"
  }
}'
```

---

## Step 4: Deploy ProxmoxMCP-Plus

Two containers, two transports — REST for VS Code/Copilot, SSE for Claude/OpenClaw:

```bash
mkdir -p ~/mcp-servers/{installations,config/proxmox-mcp-plus,logs/proxmox-mcp-plus}
cd ~/mcp-servers/installations
git clone https://github.com/RekklesNA/ProxmoxMCP-Plus.git proxmox-mcp-plus
```

Create two config files — `config.json` (STDIO/REST) and `config-sse.json` (SSE). The key difference:

```json
// config.json — REST via mcpo on 8811
"mcp": { "host": "127.0.0.1", "port": 8000, "transport": "STDIO" }

// config-sse.json — native SSE on 8812
"mcp": { "host": "0.0.0.0", "port": 8812, "transport": "SSE" }
```

Both need `"security": { "dev_mode": true }` if your Proxmox uses a self-signed cert with `"verify_ssl": false`.

Run both services via Docker Compose, then register the SSE endpoint in OpenClaw:

```bash
openclaw mcp set proxmox '{"url":"http://localhost:8812/sse","transport":"sse"}'
```

---

## Step 5: Create the Draft Post Prompt

The prompt that drives automated blog post creation lives at `~/openclaw/prompts/draft-post.md`. The critical section is `## What I want` — this is where you describe the post topic. The rest of the prompt is fixed process: clone the repo, read existing posts, write on a draft branch, run `mkdocs build --strict`, open a PR.

The `--strict` requirement is intentional. A post that breaks the build doesn't get merged. Period.

```markdown
## What I want

Write a complete post using the project template at docs/templates/project-template.md.
Follow it section by section. Do not skip sections — if a section doesn't apply,
write a one-line note explaining why and leave a <!-- TODO --> comment.
Use the voice, tone, and formatting conventions from existing posts in the same section.
Set published: false in frontmatter.
```

---

## Step 6: Create a homelab-sync Repo

A private GitHub repo for sharing MCP configs across machines:

```bash
gh repo create homelab-sync --private \
  --description "Homelab configuration sync — MCP configs, tool settings, cross-machine setup"
```

Push your MCP configs (without secrets), and use it as the communication channel between machines. On a new machine:

```bash
gh repo clone YOUR_USERNAME/homelab-sync ~/homelab-sync
```

---

## Results

Every MCP server wired up and tested end-to-end in a single session:

```
openclaw mcp list
─────────────────────────────────────
docker-mcp         → localhost:3100/sse
github-mcp         → api.githubcopilot.com/mcp/
proxmox            → localhost:8812/sse
slack              → npx stdio
youtube-transcript → mcp-youtube-transcript stdio
```

Sample Proxmox query:

> "List my nodes and VMs."

```
Node: pve — Online, 8 cores, 24.7/31.1 GB RAM (79.6%)

VMs: ansible1-4 (running), aap-controller (running, 90% mem),
prod-server01-03 (running), haos17.0 (stopped), ubuntu-gui-vscode (stopped)
```

<!-- TODO: Add screenshot: docs/assets/ai/openclaw-automated-posts/mcp-list.png -->

---

## Troubleshooting

**`scope upgrade pending approval` on `openclaw agent`**  
**Fix:** Upgrade the CLI device in `paired.json` to `operator.admin`. Don't just set `commands.ownerAllowFrom` — that's a different setting.

**ProxmoxMCP-Plus fails with `Permission denied: /logs`**  
**Fix:** Mount a logs volume in docker-compose.yml. The container writes to `/app/logs`, not `/logs` — check your config path.

**ProxmoxMCP-Plus fails with `Insecure TLS configuration blocked`**  
**Fix:** Set `"security": { "dev_mode": true }` when using `"verify_ssl": false`.

**`mkdocs build --strict` fails on image reference**  
**Fix:** Never reference an image path that doesn't exist in the repo. Use `<!-- TODO: Add screenshot -->` comments instead. This rule is now in `CLAUDE.MD`.

**Docker MCP Gateway snap Chromium conflict**  
<!-- TODO: Add note if a fix is found — snap Chromium can't write to ~/.openclaw/browser/ -->  
The Docker MCP Gateway browser profile doesn't work with snap-installed Chromium due to filesystem sandboxing. Use the Proxmox REST API directly for token creation instead.

---

## What's Next

This session established a foundation — OpenClaw with full homelab tool access and a repeatable pattern for turning completed work into published posts.

- Automated post drafts triggered by git pushes or cron — OpenClaw detects new work and drafts without being asked
- Proxmox monitoring alerts piped to Slack via OpenClaw when VMs hit memory thresholds
- An n8n workflow that routes OpenClaw draft PRs through a review-and-publish pipeline
- Connecting this to the Ansible cluster (VMs 2000-2003) for natural language playbook execution

!!! question "What would you automate first?"
    If your AI assistant had full access to your homelab, what's the first workflow you'd hand off?

---

## Related

- [AI-Powered Proxmox MCP Infrastructure](../homelab/mcp-proxmox-infrastructure.md)
- [ProxmoxMCP-Plus Setup Guide](../homelab/proxmox-mcp-setup.md)
- [How to Create a Proxmox API Token](../homelab/proxmox-api-token.md)
- [Teaching AI to Work Your Way](multi-agent-instructions.md)
