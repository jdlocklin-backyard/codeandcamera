---
title: "Automation"
description: "Automation runbooks and implementation notes for repeatable operations, debugging, and reliability improvements."
tags:
  - automation
---

# Automation

Automation projects and runbooks designed to reduce manual effort and improve operational consistency.

## Why this section matters

Automation quality is not about script count. It is about safer defaults, reproducible steps, and clear rollback paths when something breaks.

## Start with these

- **[Docker Desktop Cleanup Reference](docker-desktop-cleanup-runbook.md)** - practical reclaim workflow with verified before/after metrics.
- **[Upgrading to Slack Block Kit](slack-block-kit-upgrade.md)** - AI-assisted debugging on a real integration failure.
- **[Scheduled Proxmox Updates](n8n-scheduled-proxmox-updates.md)** - proactive status distribution with scheduled workflows.

## What you will find here

- command-by-command runbooks,
- architecture and data-flow examples,
- troubleshooting patterns validated in production-like environments,
- practical outcome summaries.

## Next action

!!! success "Start with a practical win"
    Use [Docker Desktop Cleanup Reference](docker-desktop-cleanup-runbook.md) for immediate reclaim and measurable before/after results.

!!! tip "Then level up"
    Read [Upgrading to Slack Block Kit](slack-block-kit-upgrade.md) to see AI-assisted debugging applied to a production automation workflow.

## Recent Projects

### [Docker Desktop Cleanup Reference](docker-desktop-cleanup-runbook.md)

A practical, command-by-command cleanup reference for reclaiming Docker Desktop disk and memory safely. Includes full before/after metrics, reclaim breakdown, and concrete commands from a real session using `funny_perlman`, `rancher/rancher:latest`, and `claude-memory` as validated targets.

**Tags**: Docker, cleanup, PowerShell, runbook  
**Difficulty**: Intermediate  
**Time**: 20-30 minutes

### [Upgrading to Slack Block Kit](slack-block-kit-upgrade.md)

Transforming a Proxmox Slack bot from plain text to rich, structured Block Kit messages—and the subtle n8n serialization quirk that almost derailed it. An honest look at AI-assisted debugging: what worked, what didn't, and how 15 minutes of pattern recognition saved 3+ hours of trial-and-error.

**Tags**: Slack, n8n, Block Kit, AI-assisted debugging, Proxmox  
**Difficulty**: Intermediate  
**Time**: 2 hours
