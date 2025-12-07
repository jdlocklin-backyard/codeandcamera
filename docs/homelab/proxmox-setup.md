---
title: "Setting Up Proxmox VE"
description: "Install and configure Proxmox Virtual Environment for your home lab"
tags:
  - homelab
  - virtualization
  - project
  - intermediate
status: new
---

# Setting Up Proxmox VE

Build the foundation of your home lab with this powerful, free virtualization platform.

## Overview

Proxmox Virtual Environment (VE) is an open-source server virtualization platform. It combines KVM hypervisor and LXC containers on a single platform, making it perfect for home labs where you want to run multiple VMs and containers efficiently.

---

## Project Details

| Detail | Information |
|--------|-------------|
| **Difficulty** | Intermediate |
| **Time Required** | 1-2 hours |
| **Category** | Home Lab |
| **Last Updated** | December 2025 |

---

## What You'll Learn

- Installing Proxmox VE from scratch
- Basic web UI navigation
- Creating your first VM
- Setting up containers (LXC)
- Storage configuration basics

---

## Prerequisites

- Dedicated hardware (old PC, mini PC, or server)
- USB drive (8GB+) for installation media
- Basic networking knowledge
- Understanding of virtualization concepts

---

## Hardware Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| CPU | 64-bit with VT-x/AMD-V | Intel i5/Ryzen 5 or better |
| RAM | 4GB | 16GB+ |
| Storage | 32GB SSD | 256GB+ NVMe |
| Network | 1 Gigabit NIC | 2+ NICs for VLANs |

---

## Step-by-Step Guide

### Step 1: Download Proxmox VE ISO

1. Visit [proxmox.com/downloads](https://www.proxmox.com/en/downloads)
2. Download the latest **Proxmox VE ISO Installer**
3. Verify the checksum (optional but recommended)

### Step 2: Create Bootable USB

=== "Windows (Rufus)"
    1. Download [Rufus](https://rufus.ie/)
    2. Select your USB drive
    3. Select the Proxmox ISO
    4. Click **Start**

=== "Linux (dd)"
    ```bash
    # Find your USB device
    lsblk
    
    # Write ISO to USB (replace sdX with your device)
    sudo dd if=proxmox-ve_*.iso of=/dev/sdX bs=4M status=progress
    ```

=== "macOS (balenaEtcher)"
    1. Download [balenaEtcher](https://www.balena.io/etcher/)
    2. Select ISO → Select USB → Flash

### Step 3: Install Proxmox VE

1. Boot from USB drive
2. Select **Install Proxmox VE**
3. Accept the EULA
4. Select target disk (⚠️ **This will erase all data!**)
5. Configure location and timezone
6. Set root password and email
7. Configure network:
   - **Hostname:** `proxmox.local` (or your preference)
   - **IP Address:** Static IP (e.g., `192.168.1.100`)
   - **Gateway:** Your router IP
   - **DNS:** `8.8.8.8` or your DNS server

### Step 4: Access the Web Interface

After installation and reboot:

1. Open a browser on another computer
2. Navigate to: `https://YOUR_IP:8006`
3. Accept the self-signed certificate warning
4. Login with:
   - **Username:** `root`
   - **Password:** Your chosen password
   - **Realm:** `Linux PAM`

!!! success "You're In!"
    You should now see the Proxmox VE dashboard.

---

## Post-Installation Setup

### Remove Subscription Notice

```bash title="SSH into Proxmox or use Shell in web UI"
# Edit the JavaScript file
sed -i.bak "s/data.status !== 'Active'/false/g" \
  /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js

# Clear browser cache and refresh
systemctl restart pveproxy
```

### Add Community Repository

```bash
# Disable enterprise repo (requires subscription)
mv /etc/apt/sources.list.d/pve-enterprise.list \
   /etc/apt/sources.list.d/pve-enterprise.list.disabled

# Add no-subscription repo
echo "deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription" \
  > /etc/apt/sources.list.d/pve-no-subscription.list

# Update packages
apt update && apt full-upgrade -y
```

### Enable IOMMU (for GPU Passthrough)

Edit GRUB configuration:

```bash
# For Intel CPUs
nano /etc/default/grub
# Change: GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on"

# For AMD CPUs
# Change: GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on"

# Update GRUB
update-grub
reboot
```

---

## Create Your First VM

### Step 1: Upload an ISO

1. Click **local** storage → **ISO Images**
2. Click **Upload** or **Download from URL**
3. Upload your desired OS ISO (Ubuntu, Windows, etc.)

### Step 2: Create VM

1. Click **Create VM** (top right)
2. **General:** Name your VM, note the VM ID
3. **OS:** Select your uploaded ISO
4. **System:** Keep defaults (BIOS, OVMF for UEFI)
5. **Disks:** Set size (32GB+ for Linux, 64GB+ for Windows)
6. **CPU:** Allocate cores (2-4 for most uses)
7. **Memory:** Allocate RAM (2048MB+ recommended)
8. **Network:** Keep default bridge (vmbr0)
9. **Confirm** and check "Start after created"

---

## Create Your First Container (LXC)

Containers are lighter than VMs—great for services like Pi-hole, Home Assistant, etc.

1. Download a container template:
   - **local** → **CT Templates** → **Templates**
   - Download: `ubuntu-22.04-standard`

2. Click **Create CT**
3. Configure hostname, password, template
4. Set resources (512MB RAM is often enough)
5. Configure network (DHCP or static)

---

## Recommended First Projects

| Project | Type | Use Case |
|---------|------|----------|
| Pi-hole | LXC | Network-wide ad blocking |
| Home Assistant | VM/LXC | Home automation hub |
| Plex/Jellyfin | VM | Media server |
| Ubuntu Server | VM | General purpose server |
| TrueNAS | VM | Network attached storage |

---

## Common Issues & Troubleshooting

### Can't access web UI
- Verify IP address: `ip addr show`
- Check firewall: `pvefw status`
- Ensure port 8006 isn't blocked

### VM won't start
- Check if virtualization is enabled in BIOS
- Verify enough RAM is available
- Check storage space

### Network issues in VMs
- Verify bridge configuration
- Check VLAN settings if using VLANs
- Ensure DHCP server is reachable

---

## Next Steps

- [ ] Set up backups with Proxmox Backup Server
- [ ] Configure clustering for high availability
- [ ] Implement ZFS for data integrity
- [ ] Set up VLANs for network segmentation
- [ ] Enable GPU passthrough for Plex/gaming

---

## Resources

- [Proxmox VE Documentation](https://pve.proxmox.com/pve-docs/)
- [Proxmox Forum](https://forum.proxmox.com/)
- [r/Proxmox](https://reddit.com/r/Proxmox)
- [NetworkChuck Proxmox Tutorial](https://www.youtube.com/watch?v=_u8qTN3cCnQ)
