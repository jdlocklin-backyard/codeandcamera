---
title: "Setting Up ProxmoxMCP-Plus: A Complete Installation Guide"
description: "Step-by-step guide to installing and configuring ProxmoxMCP-Plus with dual-transport Docker deployment for managing Proxmox from any AI assistant"
tags:
  - homelab
  - ai
  - automation
  - proxmox
  - docker
  - intermediate
author: "JD Locklin"
date_created: "2026-02-21"
date_updated: "2026-02-21"
difficulty: "Intermediate"
estimated_time: "1-2 hours"
---

# Setting Up ProxmoxMCP-Plus: A Complete Installation Guide

## Overview

This is the hands-on setup guide for getting ProxmoxMCP-Plus running on your machine with dual-transport Docker deployment. When you're done, you'll have two endpoints — REST on port 8811 for VS Code/Copilot and SSE on port 8812 for Claude — both talking to your Proxmox server, auto-starting on boot, with health monitoring built in.

If you want the backstory on *why* this architecture exists and how AI helped design it, read the companion post: [AI-Powered Proxmox MCP Infrastructure](mcp-proxmox-infrastructure.md). This post is purely the "shut up and show me how."

!!! success "What You'll End Up With"
    **Two Docker containers** running side-by-side, giving every major AI tool natural language access to your Proxmox environment — VMs, containers, snapshots, storage, monitoring. 31 tools total, zero manual steps after reboot.

---

## Project Details

| Detail | Information |
|--------|-------------|
| **Difficulty** | Intermediate |
| **Time Required** | 1-2 hours |
| **Category** | Home Lab + AI |
| **Last Updated** | February 2026 |

**Key Technologies:** Docker, Docker Compose, ProxmoxMCP-Plus, MCP Protocol, PowerShell

---

## What You'll Learn

- How to structure an MCP server installation with clean separation of code, config, and logs
- How to create a Proxmox API token for secure, passwordless automation
- How to set up dual-transport Docker containers (REST + SSE) from a single codebase
- How to write health checks for SSE streaming endpoints
- How to connect Claude Code, Claude Desktop, and VS Code/Copilot to your MCP server
- How to build a health monitoring script that validates everything at a glance

---

## Prerequisites

You'll need all of these before starting:

- [ ] **Docker Desktop** installed and running (with Docker Compose)
- [ ] **Proxmox VE** instance accessible on your network (I use `10.0.0.130:8006`)
- [ ] **Admin access** to Proxmox (to create an API token)
- [ ] **Git** installed
- [ ] **PowerShell** (Windows) or bash (Linux/Mac)
- [ ] **At least one AI tool**: Claude Code, Claude Desktop, or VS Code with GitHub Copilot

---

## Step 1: Create the Proxmox API Token

Before touching any code, you need a Proxmox API token. This lets the MCP server authenticate without storing your password.

1. Log into the Proxmox web UI (`https://YOUR_PROXMOX_IP:8006`)
2. Navigate to **Datacenter → Permissions → API Tokens**
3. Click **Add**
4. Configure the token:

| Field | Value |
|-------|-------|
| **User** | `root@pam` (or your admin user) |
| **Token ID** | Something descriptive — I used `opencode` |
| **Privilege Separation** | **Uncheck this** (the token needs full access) |

5. Click **Add** and **immediately copy the token value** — you won't see it again

You'll end up with two pieces of information:

```
User:        root@pam
Token Name:  opencode
Token Value:  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

!!! warning "Save That Token Value"
    Proxmox only shows the token value once. If you lose it, you'll need to delete the token and create a new one. Store it somewhere safe — you'll need it in Step 3.

---

## Step 2: Create the Directory Structure

The key design decision: **separate concerns**. Source code stays in its own git repo. Config files with credentials stay local. Logs go to their own directory. Nothing crosses the streams.

=== "Windows (PowerShell)"

    ```powershell
    # Create the root directory and subdirectories
    New-Item -ItemType Directory -Path "D:\MCP-SERVERS" -Force
    Set-Location D:\MCP-SERVERS
    "installations", "config", "logs", "scripts" | ForEach-Object {
        New-Item -ItemType Directory -Path $_ -Force
    }
    ```

=== "Linux / macOS (bash)"

    ```bash
    # Create the root directory and subdirectories
    mkdir -p ~/mcp-servers/{installations,config,logs,scripts}
    cd ~/mcp-servers
    ```

The resulting structure:

```
MCP-SERVERS/
├── installations/     # Upstream git repos (keep clean)
├── config/            # Credentials & settings (NOT in git)
├── logs/              # Runtime logs
├── scripts/           # Management scripts
├── mcp-registry.json  # Master registry of all servers
└── status.ps1         # Health check script
```

!!! tip "Why This Structure?"
    Keeping config separate from the installation means you can `git pull` to update the upstream code without worrying about overwriting your credentials. It also means your API tokens never accidentally end up in a git repo.

---

## Step 3: Clone and Configure ProxmoxMCP-Plus

Clone the upstream repo:

```bash
cd installations  # or Set-Location installations on Windows
git clone https://github.com/RekklesNA/ProxmoxMCP-Plus.git proxmox-mcp-plus
```

Create the config and log directories:

=== "Windows (PowerShell)"

    ```powershell
    New-Item -ItemType Directory -Path "D:\MCP-SERVERS\config\proxmox-mcp-plus" -Force
    New-Item -ItemType Directory -Path "D:\MCP-SERVERS\logs\proxmox-mcp-plus" -Force
    ```

=== "Linux / macOS (bash)"

    ```bash
    mkdir -p ~/mcp-servers/config/proxmox-mcp-plus
    mkdir -p ~/mcp-servers/logs/proxmox-mcp-plus
    ```

Now create two config files. They're nearly identical — the difference is the transport mode.

**REST config** (used by the API container, served through `mcpo` on port 8811):

```json title="config/proxmox-mcp-plus/config.json"
{
    "proxmox": {
        "host": "YOUR_PROXMOX_IP",
        "port": 8006,
        "verify_ssl": false,
        "service": "PVE"
    },
    "auth": {
        "user": "root@pam",
        "token_name": "YOUR_TOKEN_NAME",
        "token_value": "YOUR_TOKEN_VALUE"
    },
    "logging": {
        "level": "DEBUG",
        "format": "%(asctime)s - %(name)s - %(levelname)s - %(message)s",
        "file": "/app/logs/proxmox_mcp.log"
    },
    "mcp": {
        "host": "127.0.0.1",
        "port": 8000,
        "transport": "STDIO"
    }
}
```

**SSE config** (used by the SSE container on port 8812):

```json title="config/proxmox-mcp-plus/config-sse.json"
{
    "proxmox": {
        "host": "YOUR_PROXMOX_IP",
        "port": 8006,
        "verify_ssl": false,
        "service": "PVE"
    },
    "auth": {
        "user": "root@pam",
        "token_name": "YOUR_TOKEN_NAME",
        "token_value": "YOUR_TOKEN_VALUE"
    },
    "logging": {
        "level": "INFO",
        "format": "%(asctime)s - %(name)s - %(levelname)s - %(message)s",
        "file": "/app/logs/proxmox_mcp_sse.log"
    },
    "mcp": {
        "host": "0.0.0.0",
        "port": 8812,
        "transport": "SSE"
    }
}
```

Replace `YOUR_PROXMOX_IP`, `YOUR_TOKEN_NAME`, and `YOUR_TOKEN_VALUE` with the values from Step 1.

!!! note "Spot the Differences"
    The REST config uses `transport: "STDIO"` because `mcpo` wraps the server as a REST API. The SSE config uses `transport: "SSE"` and binds to `0.0.0.0:8812` so Docker can expose the port. The REST config has `DEBUG` logging for troubleshooting; SSE uses `INFO` to keep logs quieter.

---

## Step 4: Set Up Docker Compose

Create or verify the `docker-compose.yml` in the installation directory. This runs two services from the same Dockerfile — one per transport mode:

```yaml title="installations/proxmox-mcp-plus/docker-compose.yml"
services:
  # REST/OpenAPI via mcpo - for VS Code, Copilot (port 8811)
  proxmox-mcp-api:
    build: .
    ports:
      - "8811:8811"
    volumes:
      - ../../config/proxmox-mcp-plus:/app/proxmox-config:ro
      - ../../logs/proxmox-mcp-plus:/app/logs
    environment:
      - PROXMOX_MCP_CONFIG=/app/proxmox-config/config.json
      - API_HOST=0.0.0.0
      - API_PORT=8811
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8811/openapi.json"]
      interval: 30s
      timeout: 10s
      retries: 3
    networks:
      - proxmox-network

  # Native MCP SSE - for Claude Code, Claude Desktop (port 8812)
  proxmox-mcp-sse:
    build: .
    ports:
      - "8812:8812"
    volumes:
      - ../../config/proxmox-mcp-plus:/app/proxmox-config:ro
      - ../../logs/proxmox-mcp-plus:/app/logs
    environment:
      - PROXMOX_MCP_CONFIG=/app/proxmox-config/config-sse.json
    command: ["python", "-m", "proxmox_mcp.server"]
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL",
        "curl -s --max-time 3 http://localhost:8812/sse | grep -q 'endpoint'"]
      interval: 30s
      timeout: 10s
      retries: 3
    networks:
      - proxmox-network

networks:
  proxmox-network:
    driver: bridge
```

Key things to understand:

- **`../../config`** — the relative paths reach back to the shared `config/` directory. This is why the directory structure matters
- **`:ro`** — config is mounted read-only. The containers can't modify your credentials
- **`restart: unless-stopped`** — containers come back automatically after a reboot (as long as Docker Desktop auto-starts)
- **SSE health check** — SSE streams never close, so a normal `curl -f` will always timeout. The trick: stream for 3 seconds (`--max-time 3`) and check if the server sent its initial `endpoint` event

Build and start both containers:

```bash
cd installations/proxmox-mcp-plus
docker compose up -d --build
```

Verify they're running:

```bash
docker compose ps
```

You should see both containers with status `Up` and eventually `(healthy)`:

```
NAME                    STATUS                 PORTS
proxmox-mcp-api         Up 2 minutes (healthy) 0.0.0.0:8811->8811/tcp
proxmox-mcp-sse         Up 2 minutes (healthy) 0.0.0.0:8812->8812/tcp
```

!!! tip "First Build Takes a While"
    The initial `docker compose up --build` downloads base images and installs Python dependencies. Expect 2-5 minutes. Subsequent starts are fast since the image is cached.

---

## Step 5: Connect Your AI Tools

Each AI tool speaks a different protocol. Here's how to connect each one:

=== "Claude Code"

    Claude Code runs inside Docker itself, so `localhost` doesn't work — it points to Claude's own container. Use `host.docker.internal` instead:

    ```bash
    claude mcp add -s user --transport sse proxmox http://host.docker.internal:8812/sse
    ```

    Verify it connected:

    ```bash
    claude mcp list
    ```

    You should see:

    ```
    proxmox: ✓ Connected (SSE, 31 tools)
    ```

    !!! warning "Common Mistake: Using localhost"
        If you see `✗ Failed to connect`, you probably used `localhost` instead of `host.docker.internal`. Claude Code runs in a Docker container — `localhost` inside that container is itself, not your Windows host.

=== "Claude Desktop"

    Edit your Claude Desktop config file:

    **Windows:** `C:\Users\<username>\AppData\Roaming\Claude\claude_desktop_config.json`

    **macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`

    Add the Proxmox server:

    ```json
    {
      "mcpServers": {
        "proxmox": {
          "type": "sse",
          "url": "http://localhost:8812/sse"
        }
      }
    }
    ```

    Restart Claude Desktop to pick up the change. Claude Desktop runs natively (not in Docker), so `localhost` works fine here.

=== "VS Code / GitHub Copilot"

    Edit your VS Code MCP config:

    **Windows:** `C:\Users\<username>\AppData\Roaming\Code\User\mcp.json`

    **macOS:** `~/Library/Application Support/Code/User/mcp.json`

    Add to the `servers` object:

    ```json
    {
      "servers": {
        "proxmox": {
          "type": "http",
          "url": "http://localhost:8811"
        }
      }
    }
    ```

    Note: VS Code uses port **8811** (REST/HTTP), not 8812. It speaks OpenAPI, not SSE.

Quick reference for which tool uses which endpoint:

| AI Tool | Port | Transport | Endpoint URL |
|---------|------|-----------|-------------|
| Claude Code | 8812 | SSE | `http://host.docker.internal:8812/sse` |
| Claude Desktop | 8812 | SSE | `http://localhost:8812/sse` |
| VS Code / Copilot | 8811 | HTTP | `http://localhost:8811` |

---

## Step 6: Create the Health Check Script

You want a single command that tells you if everything is working. This script checks both endpoints and gives you a clear pass/fail:

=== "Windows (PowerShell)"

    ```powershell title="MCP-SERVERS/status.ps1"
    $ErrorActionPreference = "Continue"

    Write-Host ""
    Write-Host "MCP Server Status" -ForegroundColor Cyan
    Write-Host "=================" -ForegroundColor Cyan
    Write-Host (Get-Date -Format "yyyy-MM-dd HH:mm:ss")
    Write-Host ""

    $allGood = $true

    # Check Docker containers
    $containers = docker ps --filter "name=proxmox-mcp" `
                  --format "{{.Names}}: {{.Status}}" 2>$null
    if ($containers) {
        $containers | ForEach-Object {
            Write-Host "  OK  $_" -ForegroundColor Green
        }
    } else {
        Write-Host "  DOWN  No Proxmox MCP containers running" -ForegroundColor Red
        $allGood = $false
    }

    Write-Host ""

    # Check REST endpoint (VS Code / Copilot)
    $restOk = $false
    try {
        $null = Invoke-RestMethod `
            -Uri "http://localhost:8811/openapi.json" -TimeoutSec 5
        $restOk = $true
    } catch {}

    if ($restOk) {
        Write-Host "  OK  REST  http://localhost:8811  [VS Code / Copilot]" `
            -ForegroundColor Green
    } else {
        Write-Host "  FAIL  REST  not responding on port 8811" `
            -ForegroundColor Red
        $allGood = $false
    }

    # Check SSE endpoint (Claude Code / Desktop)
    $sseOk = $false
    $sseOutput = & curl.exe -s --max-time 3 `
                 http://localhost:8812/sse 2>$null
    if ($sseOutput -match "endpoint") { $sseOk = $true }

    if ($sseOk) {
        Write-Host "  OK  SSE   http://localhost:8812  [Claude Code / Desktop]" `
            -ForegroundColor Green
    } else {
        Write-Host "  FAIL  SSE   not responding on port 8812" `
            -ForegroundColor Red
        $allGood = $false
    }

    Write-Host ""
    if ($allGood) {
        Write-Host "All MCP servers healthy." -ForegroundColor Green
    } else {
        Write-Host "One or more servers need attention." -ForegroundColor Red
    }
    Write-Host ""
    ```

=== "Linux / macOS (bash)"

    ```bash title="mcp-servers/status.sh"
    #!/bin/bash

    echo ""
    echo "MCP Server Status"
    echo "================="
    date "+%Y-%m-%d %H:%M:%S"
    echo ""

    ALL_GOOD=true

    # Check Docker containers
    CONTAINERS=$(docker ps --filter "name=proxmox-mcp" \
                 --format "{{.Names}}: {{.Status}}" 2>/dev/null)
    if [ -n "$CONTAINERS" ]; then
        echo "$CONTAINERS" | while read line; do
            echo "  OK  $line"
        done
    else
        echo "  DOWN  No Proxmox MCP containers running"
        ALL_GOOD=false
    fi

    echo ""

    # Check REST endpoint
    if curl -sf --max-time 5 http://localhost:8811/openapi.json > /dev/null 2>&1; then
        echo "  OK  REST  http://localhost:8811  [VS Code / Copilot]"
    else
        echo "  FAIL  REST  not responding on port 8811"
        ALL_GOOD=false
    fi

    # Check SSE endpoint
    if curl -s --max-time 3 http://localhost:8812/sse 2>/dev/null | \
       grep -q "endpoint"; then
        echo "  OK  SSE   http://localhost:8812  [Claude Code / Desktop]"
    else
        echo "  FAIL  SSE   not responding on port 8812"
        ALL_GOOD=false
    fi

    echo ""
    if [ "$ALL_GOOD" = true ]; then
        echo "All MCP servers healthy."
    else
        echo "One or more servers need attention."
    fi
    echo ""
    ```

Run it and you should see:

```
MCP Server Status
=================
2026-02-21 14:30:00

  OK  proxmox-mcp-api: Up 3 hours (healthy)
  OK  proxmox-mcp-sse: Up 3 hours (healthy)

  OK  REST  http://localhost:8811  [VS Code / Copilot]
  OK  SSE   http://localhost:8812  [Claude Code / Desktop]

All MCP servers healthy.
```

<!-- TODO: Add screenshot of status script output -->

---

## Step 7: Auto-Start on Boot

The goal: zero manual steps after a reboot. You turn on your machine and the MCP server is just *there*.

**Docker Desktop** handles most of this:

1. Open Docker Desktop → **Settings → General**
2. Enable **"Start Docker Desktop when you sign in"**
3. The `restart: unless-stopped` policy in `docker-compose.yml` means both containers restart automatically when Docker starts

That's it. Docker starts on login, containers restart, AI tools reconnect to the endpoints they already have configured.

!!! tip "Verify After a Reboot"
    After your next restart, run the status script to confirm everything came back on its own. If a container didn't restart, check `docker compose logs` in the installation directory for errors.

---

## Testing It Out

Once everything is connected, try these from any of your AI tools:

> "What containers are running on my Proxmox server?"

> "How much memory is my Proxmox node using?"

> "Create a snapshot of container 112 called pre-update"

> "Show me the storage usage on local-lvm"

You should get real answers from your actual Proxmox server. If you don't, check the [Troubleshooting](#troubleshooting) section below.

---

## Troubleshooting

### Containers won't start

**Check the logs:**

```bash
cd installations/proxmox-mcp-plus
docker compose logs proxmox-mcp-api
docker compose logs proxmox-mcp-sse
```

**Common causes:**

- Config file not found — verify the volume mount paths are correct and the config files exist
- Proxmox unreachable — make sure your Proxmox IP is correct and accessible from your Docker host
- Port already in use — check if something else is using 8811 or 8812: `netstat -an | findstr "8811"`

### SSE container shows "unhealthy"

SSE connections are long-lived streams that never close. A standard health check waits for the response to complete, hits the timeout, and reports failure. Make sure your health check uses the streaming approach:

```yaml
healthcheck:
  test: ["CMD-SHELL",
    "curl -s --max-time 3 http://localhost:8812/sse | grep -q 'endpoint'"]
```

### Claude Code says "Failed to connect"

Claude Code runs inside Docker. `localhost` inside Claude's container points to itself, not your Windows host.

**Fix:** Use `host.docker.internal`:

```bash
claude mcp add -s user --transport sse proxmox http://host.docker.internal:8812/sse
```

### REST endpoint returns errors but SSE works (or vice versa)

The two containers are independent. Check the logs for the specific container that's failing:

```bash
docker compose logs proxmox-mcp-api   # REST issues
docker compose logs proxmox-mcp-sse   # SSE issues
```

Also verify the correct config file is being used — `config.json` for REST, `config-sse.json` for SSE.

### API token authentication fails

Verify your token is correct by testing directly against Proxmox:

=== "PowerShell"

    ```powershell
    $headers = @{
        Authorization = "PVEAPIToken=root@pam!YOUR_TOKEN_NAME=YOUR_TOKEN_VALUE"
    }
    Invoke-RestMethod -Uri "https://YOUR_PROXMOX_IP:8006/api2/json/version" `
        -SkipCertificateCheck -Headers $headers
    ```

=== "curl"

    ```bash
    curl -k -H "Authorization: PVEAPIToken=root@pam!YOUR_TOKEN_NAME=YOUR_TOKEN_VALUE" \
        https://YOUR_PROXMOX_IP:8006/api2/json/version
    ```

If this fails, the token is wrong or Proxmox isn't reachable. Go back to Step 1 and create a new token.

### PowerShell `curl` doesn't work like real curl

PowerShell aliases `curl` to `Invoke-WebRequest`, which has completely different syntax. Use `curl.exe` explicitly:

```powershell
# This fails — PowerShell's Invoke-WebRequest
curl -s http://localhost:8812/sse

# This works — actual curl
curl.exe -s http://localhost:8812/sse
```

---

## Complete File Reference

When you're done, your directory should look like this:

```
MCP-SERVERS/
├── installations/
│   └── proxmox-mcp-plus/          # Git clone of upstream repo
│       ├── docker-compose.yml      # Dual-service container config
│       ├── Dockerfile              # From upstream
│       └── src/                    # Server source code
├── config/
│   └── proxmox-mcp-plus/
│       ├── config.json             # REST transport config
│       └── config-sse.json         # SSE transport config
├── logs/
│   └── proxmox-mcp-plus/
│       ├── proxmox_mcp.log         # REST service logs
│       └── proxmox_mcp_sse.log     # SSE service logs
├── scripts/
│   ├── start-proxmox-docker.ps1    # Start containers
│   ├── stop-proxmox.ps1            # Stop containers
│   └── test-proxmox-connection.ps1 # Full diagnostic test
├── mcp-registry.json               # Server registry (for status.ps1)
└── status.ps1                      # Health check script
```

---

## What's Next

With the MCP server running, you have natural language access to your entire Proxmox environment. From here you might explore:

- Adding more MCP servers for other services (monitoring, DNS, backups)
- Building automation workflows that chain multiple MCP tools together
- Creating scheduled health reports that run the status script and alert on failures
- Extending the registry pattern to manage multiple MCP servers from a single dashboard

For the bigger picture on why this architecture was designed the way it was — and how AI helped at every step — see [AI-Powered Proxmox MCP Infrastructure](mcp-proxmox-infrastructure.md).

!!! question "What's Your Setup Look Like?"
    Running Proxmox? A different hypervisor? I'm curious what home lab setups people are managing with AI tools. What would you point an MCP server at first?

---

## Resources

- [ProxmoxMCP-Plus on GitHub](https://github.com/RekklesNA/ProxmoxMCP-Plus) — The open-source MCP server
- [Model Context Protocol Specification](https://modelcontextprotocol.io/) — How MCP works under the hood
- [Docker Compose Documentation](https://docs.docker.com/compose/) — Reference for the container setup
- [Proxmox API Documentation](https://pve.proxmox.com/pve-docs/api-viewer/) — Full API reference for what the MCP server can do
- [AI-Powered Proxmox MCP Infrastructure](mcp-proxmox-infrastructure.md) — The companion post covering architecture and design decisions
