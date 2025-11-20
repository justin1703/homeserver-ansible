# Homeserver Ansible Setup

This Ansible project automates the installation, configuration, and management of a private home server running **Debian 13**.  
It provides a lightweight, secure setup for Docker containers and bare-metal services, including monitoring, SNMP integration, and automatic updates.

---
> ⚠️ **Warning:** There are currently some Issues with Docker Version 29, please use the latest 28.xx Version.
---
## Goals

- Reduce unnecessary services and complexity  
- Unified deployment across new servers  
- Secure management of Docker containers, bare-metal services, and monitoring  
- Security-focused setup: minimal root usage, hardened SSH access  
- Easy re-deployment on new hardware or after reinstallation  

---

## Future Goals

Planned extensions and improvements for security, efficiency, and maintenance:

**Security & Hardening**

 - Fail2Ban for protection against brute-force attacks  
 - Automatic security updates (unattended-upgrades)

**Network & Firewall**

 - VPN access (WireGuard) for remote management  

**Energy Efficiency**

 - CPU power management (cpufrequtils, governor tuning)  
 - HDD spin-down via hdparm  
 - Automatic shutdown via NUT server during power outages  

**Monitoring & Transparency**

 - Extended Checkmk checks (SMART, temperature, load)  
 - SNMP integration for network devices and UPS  
 - Discord notifications for system or container failures  
 - Docker health checks & Watchtower reporting  

**Structure & Scalability**

 - Further modularization of roles (e.g., firewall, backup, security)

---

## Server Hardware & Environment

The home server has been upgraded to modern, efficient hardware to achieve a good balance of **performance, energy efficiency, and future readiness**.

### Hardware Specifications

| Component | Description |
|------------|-------------|
| **CPU** | AMD Ryzen 5 5600G – APU with integrated graphics and excellent energy efficiency |
| **RAM** | 128 GB DDR4 @ 3200 MT/s – originally for many VMs under Proxmox, now provides plenty of headroom for containers and caching |
| **System Drive** | 2 TB NVMe SSD – high I/O performance for system, Docker containers, and databases |
| **Data Storage** | 2 × 4 TB HDD – plenty of space for media, backups, and persistent data |
| **Network Card** | Dual-port 10 Gbit SFP+ – ideal for high data throughput in the home network or between NAS and backup systems |
| **Mainboard** | Gigabyte B550M AORUS ELITE AX – stable, well-equipped, and future-proof |
| **Cooler** | NZXT low-profile CPU cooler – quiet and space-saving |
| **Case** | Jonsbo N4 NAS case – compact design with room for multiple 3.5" HDDs |

### System Environment

- **Operating System:** Debian 13 (Trixie)  
- **Management:** Fully automated via Ansible  
- **Services:** Mix of Docker containers and bare-metal applications  
- **Monitoring:** Checkmk with SNMP integration  
- **Network:** Static IP, 10 Gbit, SSH key access  

---

## From Proxmox to Debian

The server originally ran under **Proxmox VE** with multiple virtual machines.  
The move to **Debian 13** was made to simplify the setup and reduce resource usage.

**Reasons for the switch:**
- Eliminate unnecessary services and virtualization overhead  
- Direct hardware access without a VM layer  
- Less complexity for updates and backups  
- Potentially lower power consumption  
- Better integration with Ansible  

The new setup runs natively on Debian and uses Docker containers for all modular services — lightweight, consistent, and easy to maintain.

---

## Supported Services

### Docker Containers

| Service | Description |
|----------|-------------|
| **Omada Controller** | Management of network devices (access points, switches, routers) |
| **Peanut** | Frontend for visualizing UPS data (via NUT server) and media status |
| **Nextcloud** | Private cloud for files, calendars, contacts, and backups |
| **Homepage** | Overview page for local services with icons and links |
| **Nginx Proxy Manager** | Reverse proxy with SSL certificate management |
| **Home Assistant** | Smart home automation and control |
| **Watchtower** | Automatic updates for all containers |
| **Pi-hole** | Network-wide DNS filter and ad blocker |
| **Checkmk** | Monitoring platform for hosts, containers, and services (including agent installation) |
| **Paperless** | Digital document management with OCR, tagging, and searchable archive |

### Bare-Metal

| Service | Description |
|----------|-------------|
| **Plex Media Server** | Local media server for movies, series, and music |
| **NUT Server** | Communication with UPS, providing status data via SNMP/Peanut |

---

## Current Problems
This section provides all necessary UFW rules and other important changes which I need to add into this project afterwards.

### Allow Nginx Proxy Manager to reach Nextcloud, this need to be done for every Service you want to reach through the Proxy-Manager:
```bash
ufw allow from 172.21.0.0/16 to any port 8080 proto tcp
```

### Allow external access for Nextcloud:
```bash
ufw allow 80/tcp
ufw allow 443/tcp
```

### Allow Peanut to reach NUT-Server:
```bash
ufw allow from 172.21.0.2/16 to any port 3493 proto tcp
```

---

## Installation

### Requirements

- Debian 13 with SSH access  
- User with `sudo` privileges  
- Ansible installed on the control machine  
- SSH key authentication configured  

---

### Step 1: Add the User which should execute the playbook to sudo

```bash
sudo usermod -aG sudo <username>
sudo visudo
<username> ALL=(ALL) NOPASSWD:ALL
```

### Optional: Setup RAID 1 for storage

> ⚠️ **Warning:** This will delete all data on `/dev/sda` and `/dev/sdb`. Only proceed if you are sure.

```bash
# 1. Create GPT partition tables on both drives
parted /dev/sda -- mklabel gpt
parted /dev/sda -- mkpart primary 0% 100%

parted /dev/sdb -- mklabel gpt
parted /dev/sdb -- mkpart primary 0% 100%

# 2. Create RAID 1 array with both partitions
mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sda1 /dev/sdb1

# Check RAID sync progress
cat /proc/mdstat

# 3. Format the RAID array with ext4 filesystem
mkfs.ext4 /dev/md0

# 4. Create mount point and mount the RAID
mkdir -p /mnt/data
mount /dev/md0 /mnt/data
```

### Step 2: Clone the Repository

```bash
git clone <REPO_URL>
cd <REPO_NAME>
```

### Step 3: Execute the playbook

```bash
ansible-playbook -i inventory.ini playbooks/server-install.yml --ask-vault-pass
```
