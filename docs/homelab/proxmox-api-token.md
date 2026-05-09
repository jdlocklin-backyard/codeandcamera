---
title: "How to Create a Proxmox API Token"
description: "A quick guide to generating a Proxmox VE API token for secure, passwordless automation — no more hardcoding your root password into scripts."
tags:
  - homelab
  - proxmox
  - automation
  - security
  - beginner
author: "JD Locklin"
date_created: "2026-05-09"
date_updated: "2026-05-09"
difficulty: "Beginner"
estimated_time: "5 minutes"
---

# How to Create a Proxmox API Token

## Overview

If you're automating anything against Proxmox — MCP servers, scripts, n8n workflows, Ansible playbooks — you need an API token. Not your root password. An API token.

Tokens are:

- **Scoped** — you control exactly what they can do
- **Revocable** — compromise one, delete it, mint a new one
- **Auditable** — Proxmox logs actions per token, not just per user

This takes five minutes and you'll never paste your root password into a config file again.

---

## Prerequisites

- Proxmox VE instance running (any recent version)
- Admin access to the web UI
- Something that needs to talk to Proxmox (MCP server, script, etc.)

---

## Step 1: Log Into the Web UI

Navigate to your Proxmox UI in a browser:

```
https://YOUR_PROXMOX_IP:8006
```

Log in as `root` or any user with administrator privileges.

!!! tip "Self-Signed Cert Warning"
    Most homelab Proxmox installs use a self-signed certificate. Your browser will warn you — click through or add a permanent exception. You can replace it with a real cert later via Let's Encrypt if you want.

---

## Step 2: Navigate to API Tokens

In the left sidebar:

> **Datacenter** → **Permissions** → **API Tokens** → **Add**

---

## Step 3: Fill In the Token Details

The Add dialog has a few fields:

| Field | What to Put |
|---|---|
| **User** | `root@pam` (or your admin user) |
| **Token ID** | Something descriptive — e.g. `automation`, `rook`, `n8n` |
| **Privilege Separation** | Uncheck this for full access |
| **Expire** | Leave blank for no expiry, or set a date for rotation discipline |

**Privilege Separation** is the key decision here. When checked, the token operates with a separate, more restricted permission set that you manage independently. When unchecked, the token inherits the full permissions of the user it belongs to.

For homelab automation where you trust the thing calling the API, unchecked is fine. For anything exposed to a network you don't fully control, consider scoping it down.

---

## Step 4: Copy the Secret — Right Now

After clicking **Add**, Proxmox shows you:

```
Token ID:  root@pam!your-token-name
Secret:    xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

**Copy the secret immediately.** Proxmox will never show it again. If you lose it, you have to delete the token and create a new one — the secret cannot be retrieved.

Put it somewhere safe before closing the dialog:

- Password manager (1Password, Bitwarden, etc.)
- Your secrets manager of choice
- Somewhere that is not a plaintext file in a git repo

---

## Step 5: Use It

Most tools expect the token in one of two formats:

**Header (HTTP):**
```
Authorization: PVEAPIToken=root@pam!your-token-name=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

**Config file (split):**
```json
{
  "auth": {
    "user": "root@pam",
    "token_name": "your-token-name",
    "token_value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  }
}
```

Environment variables are a clean middle ground — keeps the secret out of config files while staying accessible:

```bash
export PROXMOX_TOKEN_ID="root@pam!your-token-name"
export PROXMOX_TOKEN_SECRET="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

---

## Revoking or Rotating a Token

When you need to revoke a token (leaked, rotated, tool decommissioned):

> **Datacenter** → **Permissions** → **API Tokens** → select token → **Remove**

There's no rotation in the UI — removal and re-creation is the flow. Name your tokens after what uses them (`rook`, `n8n`, `ansible`) so you know exactly what breaks when you remove one.

---

## Related Posts

- [AI-Powered Proxmox MCP Infrastructure](mcp-proxmox-infrastructure.md)
- [ProxmoxMCP-Plus Setup Guide](proxmox-mcp-setup.md)
- [Scheduled Proxmox Updates with n8n](../automation/n8n-scheduled-proxmox-updates.md)
