---
title: "Scheduled Proxmox Updates in Slack: Building Self-Service n8n Workflows"
description: "How AI helped debug and deploy two scheduled n8n workflows that automatically post Proxmox status updates and available commands to Slack"
tags:
  - automation
  - n8n
  - slack
  - proxmox
  - ai-assisted
  - intermediate
published: true
author: "JD Locklin"
date_created: "2026-04-19"
date_updated: "2026-04-19"
difficulty: "Intermediate"
estimated_time: "1 hour"
---

# Scheduled Proxmox Updates in Slack: Building Self-Service n8n Workflows

My Proxmox Slack bot was great for on-demand queries — `/proxmox status`, `/proxmox vms`, etc. But I wanted proactive updates. Why wait for someone to ask when the bot could just tell us what's available every Monday morning? And why not get regular status updates throughout the day?

I built two new scheduled workflows:
1. **Weekly help reminder** (Mondays at 9 AM) — posts all available bot commands
2. **6-hour status updates** — posts full cluster status every 6 hours

AI didn't just help me build these faster. It helped me debug three critical issues that would have taken hours to track down manually.

!!! success "The Result"
    **BEFORE:** Manual checks whenever someone needed Proxmox info
    
    **AFTER:** Automated status in Slack every 6 hours + weekly command reminders
    
    **Time to build:** 1 hour (with AI assistance for debugging)

---

## Project Details

| Detail | Information |
|--------|-------------|
| **Difficulty** | Intermediate |
| **Time Required** | 1 hour |
| **Category** | Automation + AI |
| **Last Updated** | April 2026 |

**Key Technologies:** n8n, Slack API, Proxmox API, Block Kit, Cron scheduling

---

## What You'll Learn

- How to clone and modify existing n8n workflows
- Debugging Proxmox API endpoint errors (LXC vs cluster/resources)
- Fixing Slack authentication issues in n8n HTTP Request nodes
- How AI speeds up n8n troubleshooting (15 minutes vs hours)

---

## The Manual Workflow

Before these scheduled workflows, our team had to:

1. Remember to check Proxmox manually
2. SSH or open the web UI for status
3. Ask in Slack "what can the Proxmox bot do again?"
4. Re-discover commands every time someone new joined

```mermaid
flowchart LR
    subgraph before["⛔ BEFORE"]
        direction TB
        B1["Need Proxmox<br/>info"] --> B2["Ask in Slack<br/>or SSH manually"]
        B2 --> B3["Wait for<br/>someone to help"]
    end

    subgraph after["✅ AFTER"]
        direction TB
        A1["Automated<br/>status posts"] --> A2["Info available<br/>proactively"]
        A2 --> A3["Self-service<br/>for everyone"]
    end

    before ~~~ after
```

---

## How AI Helped

This project involved cloning an existing workflow, modifying it for scheduled triggers, and debugging three show-stopping issues. Here's where AI made the difference:

### 1. Architectural Guidance

**Me:** "I want to create two new workflows: one that posts help commands weekly, and one that posts status every 6 hours."

**AI:**
- Suggested cloning the existing "Proxmox Status - Scheduled" workflow as a template
- Identified which nodes to keep, which to modify
- Recommended using the same authentication pattern as the working bot

**Without AI:** I would have built from scratch, likely missing the authentication gotcha that broke things later.

### 2. Debugging the LXC Container Endpoint (15 minutes with AI vs hours manually)

The "Get Containers" node was failing with HTTP 400:

```json
{
  "errors": {
    "type": "value 'lxc' does not have a value in the enumeration 'vm, storage, node, sdn'"
  }
}
```

**AI immediately identified:**
- The `/cluster/resources?type=lxc` endpoint doesn't accept `lxc` as a valid type
- The correct endpoint is `/nodes/pve/lxc` (node-specific)
- Showed me where this was working in the existing slash command workflow

**Time saved:** Easily an hour of reading Proxmox API docs and trial-and-error.

### 3. Fixing Slack Authentication

The "Post to Slack" node was returning `not_authed` even though I'd copied it from a working workflow.

**The problem:** The node had an invalid "Specify Headers" value (`using-fields-below` with hyphens, not the proper dropdown option). This prevented the Authorization header field from even rendering.

**AI spotted it:** "The dropdown needs to be set to 'Using Fields Below' (the actual option), not a literal string."

**Without AI:** I would have been staring at "not_authed" errors for a long time, not realizing the header wasn't being sent at all.

---

## System Architecture

Here's how the two workflows fit into the existing Proxmox Slack bot infrastructure:

```mermaid
flowchart TB
    subgraph triggers["⏰ Triggers"]
        T1["Weekly Cron<br/>Mon 9 AM"]
        T2["6-Hour Cron<br/>*/6 hours"]
    end

    subgraph workflows["🔄 n8n Workflows"]
        W1["Proxmox Bot Help<br/>Scheduled"]
        W2["Proxmox Status<br/>Every 6 Hours"]
    end

    subgraph apis["🌐 APIs"]
        P["Proxmox API<br/>10.0.0.130:8006"]
        S["Slack API<br/>chat.postMessage"]
    end

    subgraph output["💬 Slack"]
        C["#proxmox<br/>channel"]
    end

    T1 --> W1
    T2 --> W2
    W1 --> S
    W2 --> P
    W2 --> S
    P --> W2
    S --> C
```

---

## Building the Workflows

### Prerequisites

- Existing n8n instance (mine runs at `http://10.0.0.218:5678`)
- Slack bot with `chat:write` permissions
- Proxmox API token (`PVEAPIToken=root@pam!n8n=...`)
- Access to an existing working Proxmox workflow (for cloning)

### Workflow 1: Weekly Help Reminder

**What it does:** Posts all available Proxmox bot commands to Slack every Monday at 9 AM.

**Schedule:** Cron expression `0 9 * * 1` (9 AM on Mondays)

**Key nodes:**
1. **Schedule Trigger** — fires weekly
2. **Format Help Message** — creates Block Kit formatted message
3. **Post to Slack** — sends to `#proxmox` channel

**Authentication pattern (critical):**
```yaml
Node: Post to Slack (HTTP Request)
  URL: https://slack.com/api/chat.postMessage
  Method: POST
  Authentication: none
  Send Headers: Using Fields Below
    - Name: Authorization
      Value: Bearer xoxb-YOUR-SLACK-TOKEN
  Send Body: Using JSON
    Expression: ={{
      {
        "channel": "#proxmox",
        "text": "Proxmox Bot Commands",
        "blocks": $json.blocks
      }
    }}
```

### Workflow 2: 6-Hour Status Updates

**What it does:** Posts full Proxmox cluster status (nodes, VMs, containers) every 6 hours.

**Schedule:** Cron expression `0 */6 * * *`

**Key nodes:**
1. **Schedule Trigger** — fires every 6 hours
2. **Get Nodes** (HTTP) — `/api2/json/cluster/resources?type=node`
3. **Get VMs** (HTTP) — `/api2/json/cluster/resources?type=vm`
4. **Get Containers** (HTTP) — `/api2/json/nodes/pve/lxc` ⚠️ *Not* `/cluster/resources?type=lxc`
5. **Format Message** — merges data into Block Kit
6. **Post to Slack** — sends to `#proxmox`

**The critical fix (Get Containers node):**

=== "❌ Wrong (400 error)"

    ```yaml
    URL: https://10.0.0.130:8006/api2/json/cluster/resources?type=lxc
    ```

    Error: `value 'lxc' does not have a value in the enumeration`

=== "✅ Correct"

    ```yaml
    URL: https://10.0.0.130:8006/api2/json/nodes/pve/lxc
    Authorization: PVEAPIToken=root@pam!n8n=YOUR_TOKEN
    SSL: Allow unauthorized certificates
    ```

---

## The Three Show-Stopping Bugs

### Bug 1: Invalid Proxmox Endpoint for LXC Containers

**Symptom:** HTTP 400 Bad Request from Proxmox API

**Error message:**
```json
{
  "errors": {
    "type": "value 'lxc' does not have a value in the enumeration 'vm, storage, node, sdn'"
  }
}
```

**Root cause:** The `/cluster/resources` endpoint only accepts `vm`, `storage`, `node`, or `sdn` as the `type` parameter. LXC containers require a different endpoint.

**Fix:** Use `/nodes/pve/lxc` instead (node-specific endpoint).

**How AI helped:** Immediately identified the correct endpoint from the Proxmox API documentation and pointed me to the working implementation in the existing slash command workflow.

---

### Bug 2: Slack Authentication Not Working (Silent Failure)

**Symptom:** Slack API returning `not_authed` even though the workflow looked correct

**Root cause:** The "Specify Headers" dropdown had an invalid literal string value (`using-fields-below` with hyphens) instead of the actual dropdown option "Using Fields Below". This prevented the Authorization header field from rendering at all.

**Fix:**
1. Reset the dropdown to the valid option "Using Fields Below"
2. Add the Authorization header: `Bearer xoxb-YOUR-TOKEN`

**How AI helped:** Spotted the invalid dropdown value immediately. Without AI, I would have been debugging "why isn't my header being sent?" for a long time.

---

### Bug 3: Slack Channel Mismatch

**Symptom:** Messages posting to the wrong channel

**Root cause:** The cloned workflow was still targeting `#homelab` instead of `#proxmox`

**Fix:** Updated the JSON body expression from `"channel": "#homelab"` to `"channel": "#proxmox"`

**How AI helped:** Noticed the channel mismatch during the review and suggested the fix before I even deployed.

---

## Results

<!-- TODO: Add screenshot of scheduled help message in Slack -->
<!-- File: docs/assets/automation/n8n-scheduled-proxmox/help-message.png -->

<!-- TODO: Add screenshot of 6-hour status update in Slack -->
<!-- File: docs/assets/automation/n8n-scheduled-proxmox/status-update.png -->

**Both workflows are now active and running successfully:**

- ✅ Weekly help reminders posting to `#proxmox` every Monday at 9 AM
- ✅ Status updates posting every 6 hours with live cluster data
- ✅ All nodes executing successfully (green checkmarks)
- ✅ No authentication errors, no endpoint failures

---

## What's Next

These scheduled workflows create a foundation for more proactive automation:

- **Smart alerting** — notify when a container goes down or CPU spikes
- **Capacity tracking** — trending posts showing storage/memory usage over time
- **Multi-node support** — iterate through all nodes for containers (not just `pve`)
- **Credential consolidation** — migrate the Slack bot token to a shared n8n Credential

I'm actively exploring how to make these workflows more intelligent — maybe using AI to analyze the status data and only alert when something looks off, rather than posting every 6 hours regardless.

!!! question "What Would You Automate?"
    What proactive updates would be most useful in your home lab? Status checks? Resource alerts? Daily summaries?

---

## Resources

- [n8n Documentation](https://docs.n8n.io/)
- [Slack Block Kit Builder](https://app.slack.com/block-kit-builder)
- [Proxmox API Documentation](https://pve.proxmox.com/pve-docs/api-viewer/)
- [Original Proxmox Slack Bot Post](../homelab/proxmox-slack-bot.md)
- [Block Kit Upgrade Post](slack-block-kit-upgrade.md)

---

**Difficulty**: Intermediate | **Time**: 1 hour (with AI assistance)
