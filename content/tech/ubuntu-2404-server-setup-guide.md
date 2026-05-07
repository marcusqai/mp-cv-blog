---
title: "Ubuntu 24.04 LTS — One-Click Server Setup Guide"
date: 2026-05-07T01:35:00+08:00
draft: false
tags:
  - tech
  - ubuntu
  - docker
  - server
summary: "A ready-to-use bash script for fresh Ubuntu 24.04 LTS servers — timezone, mirrors, SSH, Docker, and essential tools."
description: "A ready-to-use bash script for fresh Ubuntu 24.04 LTS servers — timezone, mirrors, SSH, Docker, and essential tools."
---

## Overview

This guide provides a single bash script that handles the most common post-installation tasks on a fresh **Ubuntu 24.04 LTS** server. It's optimized for deployments in **Hong Kong and mainland China**, with regional mirror acceleration and essential tooling pre-configured.

> ⚠️ **Security Note:** This script enables root login and password authentication for initial setup convenience. After completing your setup, consider disabling them in favor of SSH key-based authentication for production environments.

---

## Step 1: Create the Setup Script

Create the script file:
```bash
nano setup.sh
```

Make it executable:
```bash
chmod +x setup.sh
```

---

## Step 2: The Script

Copy the following into `setup.sh`:

```bash
#!/bin/bash

# 1. Set timezone to Hong Kong (HKT) — persistent
echo "Setting timezone to Asia/Hong_Kong..."
timedatectl set-timezone Asia/Hong_Kong
echo "Current system time: $(date)"

# 2. Switch package mirrors to Aliyun (optimized for Hong Kong & China)
echo "Switching package sources to Aliyun mirror..."
if [ -f /etc/apt/sources.list.d/ubuntu.sources ]; then
 cp /etc/apt/sources.list.d/ubuntu.sources /etc/apt/sources.list.d/ubuntu.sources.bak
 sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list.d/ubuntu.sources
 sed -i 's/security.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list.d/ubuntu.sources
else
 cp /etc/apt/sources.list /etc/apt/sources.list.bak
 sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list
 sed -i 's/security.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list
fi

# 3. Enable root login and password authentication
echo "Configuring SSH permissions..."
sed -i 's/#PermitRootLogin.*/PermitRootLogin yes/' /etc/ssh/sshd_config
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config
systemctl restart ssh

# 4. System update & install essential tools (netstat, build-essential, etc.)
echo "Updating system and installing development tools..."
export DEBIAN_FRONTEND=noninteractive
apt-get update && apt-get upgrade -y
apt-get install -y net-tools build-essential git curl wget vim

# 5. Install Docker (stable release)
echo "Installing Docker..."
apt-get install -y ca-certificates gnupg
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
chmod a+r /etc/apt/keyrings/docker.gpg

echo \
 "deb [arch=\"$(dpkg --print-architecture)\" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
 "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
 tee /etc/apt/sources.list.d/docker.list > /dev/null

apt-get update
apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

systemctl enable docker
systemctl start docker

echo "---------------------------------------------------------"
echo "Setup complete!"
echo "Timezone: Asia/Hong_Kong (HKT)"
echo "Package source: Aliyun Mirror"
echo "Run 'sudo passwd root' to set your root password."
echo "---------------------------------------------------------"
```

---

## Step 3: Run the Script

Execute with root privileges:
```bash
sudo ./setup.sh
```

Set the root password:
```bash
sudo passwd root
```

---

## What This Script Does

| Step | Action |
|------|--------|
| **1** | Set timezone to Asia/Hong_Kong (HKT) |
| **2** | Switch apt mirrors to Aliyun for faster downloads in HK/China |
| **3** | Enable root login and password auth (for initial setup) |
| **4** | Update system + install essential tools (net-tools, build-essential, git, curl, wget, vim) |
| **5** | Install Docker (stable) with docker-compose plugin |

---

## Security Recommendations

After initial setup, consider the following:

1. **Switch to SSH key-based authentication** — see [my SSH key pair guide](/tech/ssh-key-pair-authentication/)
2. **Disable root login** — set `PermitRootLogin no` in `/etc/ssh/sshd_config`
3. **Disable password authentication** — set `PasswordAuthentication no`
4. **Set up a firewall** — `ufw enable` with appropriate rules

This script is designed for **initial convenience**, not production hardening. Adjust settings according to your security requirements.
