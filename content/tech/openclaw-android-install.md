---
title: "How to Install OpenClaw on Android Phone — Complete Setup Guide with Real Problems & Fixes"
date: 2026-05-09T17:28:56+08:00
draft: false
tags:
  - tech
  - openclaw
  - android
summary: "A step-by-step guide to installing OpenClaw on Android via Termux, enabling WiFi ADB, setting up SSH, and pairing with a Gateway server — with solutions to 6 real-world problems."
description: "Complete guide to installing OpenClaw AI agent framework on Android phone via Termux, covering WiFi ADB setup, SSH access, Gateway pairing, and fixes for 6 common problems including MIUI restrictions and Node pairing failures."
---

## Introduction

OpenClaw is a powerful AI agent framework that can run on Android phones, turning a mobile device into a personal AI assistant node. This guide walks you through the complete installation process, covering both Termux + WiFi ADB method and direct installation, with real problems we encountered and their solutions.

**What You'll Need:**
- Android phone (MIUI/custom ROM tested)
- Termux app installed
- A Linux server or PC for remote access
- WiFi network (same LAN)

---

## Part 1: Install OpenClaw on Android via Termux

### Step 1: Install Termux

1. Download Termux from F-Droid (NOT Google Play — the Play version is outdated)
   - F-Droid: https://f-droid.org/en/packages/com.termux/
2. Grant storage permission after installation:
   ```
   termux-setup-storage
   ```

### Step 2: Install Required Packages

```bash
pkg update && pkg upgrade -y
pkg install openclaw proot-distro termux-api -y
```

### Step 3: Install OpenClaw via npm

```bash
npm install -g openclaw
```

### Step 4: Configure OpenClaw

```bash
openclaw configure
```

Follow the interactive setup:
- Choose "Local" mode (on the phone itself)
- Set a gateway token (remember this!)
- Configure channels (Telegram recommended for easy access)

### Step 5: Start the Gateway

```bash
openclaw gateway run
```

Check if it's running:
```bash
curl -s http://localhost:8888/health
# Should return: {"ok":true,"status":"live"}
```

---

## Part 2: Enable Wireless ADB (For Remote Access)

Android by default only allows USB debugging. To enable WiFi ADB:

### Step 1: Enable Developer Options
- Go to Settings → About Phone → Tap "MIUI Version" 7 times
- Go to Settings → Additional Settings → Developer Options

### Step 2: Enable USB Debugging
- Turn ON "USB Debugging"
- Turn ON "Wireless Debugging" (Android 11+)

### Step 3: Get WiFi ADB Port
- On the phone, note the port shown under "Wireless Debugging" (e.g., `::5555` or specific IP:port)

### Step 4: Connect from Your PC

```bash
adb connect <phone-ip>:<port>
# Example: adb connect 192.168.1.100:5555
adb devices
# Should show: 192.168.1.100:5555 device
```

---

## Part 3: Install Apps Remotely via ADB

### Install Termux API (for camera, sensors, etc.)

```bash
# Download APK first, then install
adb install termux-api.apk
```

> **Note:** On MIUI devices, `pm install` from within Termux may be blocked due to XSpace/Multi-user restrictions. Use ADB instead:
> ```bash
> adb install <app.apk>
> ```

### Grant Permissions

```bash
adb shell pm grant com.termux.api android.permission.CAMERA
adb shell pm grant com.termux.api android.permission.RECORD_AUDIO
```

### Test Camera

```bash
termux-camera-photo -c 1 /sdcard/test.jpg
```

---

## Part 4: SSH Access to Your Phone

For persistent remote access without ADB:

### Step 1: Install sshd in Termux

```bash
pkg install openssh -y
sshd
```

### Step 2: Set Password

```bash
passwd
# Enter a strong password
```

### Step 3: Find IP and Connect

```bash
ifconfig
# Note the wlan0 IP (e.g., 192.168.1.100)
# Default port: 8022
```

From your PC:
```bash
ssh user@192.168.1.100 -p 8022
```

### Auto-Start sshd on Boot

Create `/data/data/com.termux/files/home/.termux/boot/start-sshd.sh`:

```bash
#!/bin/bash
sshd
```

Make it executable:
```bash
chmod +x /data/data/com.termux/files/home/.termux/boot/start-sshd.sh
```

---

## Part 5: Pair OpenClaw Node with Gateway

### On the Phone (Node)

```bash
openclaw node run --host <gateway-server-ip> --port 8888
```

### On the Gateway Server (Main)

Check pending nodes:
```bash
openclaw nodes list
```

Approve the pending pairing request:
```bash
openclaw nodes approve <request-id>
```

Or manually by editing the pairing files (detailed in Problem 4 below).

### Verify Connection

```bash
openclaw nodes describe <node-id>
# Should show: Status: paired · connected
```

---

## Part 6: Real Problems We Faced and How to Fix

### Problem 1: WiFi ADB Port Changes After Reboot

**Symptom:**
```
adb connect 192.168.1.100:5555
# error: no devices/emulators found
```

**Cause:** On MIUI and many Android devices, the WiFi ADB port is random after each reboot or toggle.

**Fix:**
1. Re-enable "Wireless Debugging" in Developer Options after each reboot
2. Note the new port each time
3. Create a script on your PC:
```bash
#!/bin/bash
adb connect 192.168.1.100:$(adb pair 192.168.1.100 2>/dev/null | grep -oP '\d+$')
```

**Alternative:** Use Termux sshd (port 8022) instead of ADB for most operations. ADB is only needed for app installation and initial setup.

---

### Problem 2: pm install Blocked on MIUI (XSpace Restriction)

**Symptom:**
```
$ pm install app.apk
Error: INSTALL_FAILED_NO_MATCHING_ABIS
# OR simply silently fails
```

**Cause:** MIUI's XSpace/Multi-user feature restricts `pm install` from non-system apps.

**Fix:**
1. Use ADB to install instead:
```bash
adb install app.apk
```
2. If you must use Termux, try:
```bash
sh /data/data/com.termux/files/usr/libexec/termux/termux-boot-wrapper.sh install app.apk
```

---

### Problem 3: USB Debugging Gets Disabled After Reboot

**Symptom:** USB debugging is greyed out or keeps getting disabled.

**Cause:** MIUI security settings reset USB debugging after reboot in some cases.

**Fix:**
1. Go to Settings → Additional Settings → Developer Options
2. Ensure "USB Debugging" stays ON
3. For permanent solution, consider rooting the device
4. As workaround: use sshd in Termux (port 8022) — this survives reboots

---

### Problem 4: OpenClaw Node Pairing Keeps Failing

**Symptom:**
```
node host gateway connect failed: device pairing required
node host gateway reconnect paused after close (1008): pairing required
```

**Cause:** The Gateway doesn't have the Node's device registered in its pairing system.

**Fix:**

**Method A — Use the CLI (if versions match):**
```bash
# On Gateway server
openclaw nodes list
# Find the pending request ID
openclaw nodes approve <request-id>
```

**Method B — Manual file edit (if CLI approval fails due to version mismatch):**

1. On the Gateway server, find the pending entry:
```bash
cat /root/.openclaw/devices/pending.json
# Look for the device entry with the correct IP
```

2. Copy the entry to paired.json using Python:
```python
import json

with open('/root/.openclaw/devices/pending.json') as f:
    pending = json.load(f)
with open('/root/.openclaw/devices/paired.json') as f:
    paired = json.load(f)

for key, entry in pending.items():
    if entry.get('remoteIp') == 'YOUR_PHONE_IP':
        entry['approvedAtMs'] = entry.get('ts', 1234567890)
        paired[key] = entry
        break

with open('/root/.openclaw/devices/paired.json', 'w') as f:
    json.dump(paired, f, indent=2)
with open('/root/.openclaw/devices/pending.json', 'w') as f:
    json.dump({}, f)
```

3. Restart the Gateway:
```bash
kill $(pgrep openclaw) && openclaw gateway run
```

**Note:** Version mismatch between Node and Gateway can cause pairing API failures. Upgrade both to the same version:
```bash
npm install -g openclaw@latest
```

---

### Problem 5: Termux Boot Scripts Not Running

**Symptom:** SSH daemon doesn't start after rebooting the phone.

**Cause:** The boot script location or permissions are incorrect.

**Fix:**
1. Ensure the script is in the correct location:
```
/data/data/com.termux/files/home/.termux/boot/
```

2. Make sure it has no file extension:
```
start-sshd.sh  ✅
start-sshd.sh.txt  ❌
```

3. Create the script properly:
```bash
mkdir -p ~/.termux/boot
cat > ~/.termux/boot/start-sshd.sh << 'EOF'
#!/bin/bash
sshd
EOF
chmod +x ~/.termux/boot/start-sshd.sh
```

4. Test by running the script manually first.

---

### Problem 6: Node Version vs Gateway Version Mismatch

**Symptom:**
```
node host gateway connect failed: connect ECONNREFUSED
# OR
nodes approve failed: GatewayClientRequestError: unknown requestId
```

**Cause:** The Node and Gateway are running different versions of OpenClaw with incompatible pairing protocols.

**Fix:**
Upgrade both to the same version:
```bash
# On phone (via Termux)
npm install -g openclaw@latest

# On Gateway server
npm install -g openclaw@latest
```

Check versions:
```bash
openclaw --version
```

---

## System Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                    Main Gateway Server                    │
│              (Linux VM, 192.168.1.x:8888)                │
│                                                          │
│   OpenClaw Gateway ─── Telegram Bot ─── User             │
│         ↑                                                │
│         │ ws:// connection (password auth)                │
│         │                                                │
│         └──────────┬───────────────────────────────────┐│
│                    │                                    ││
│           OpenClaw Node (Paired)                       ││
│           Android Phone (Termux)                        ││
│           WiFi ADB (port 5555)                         ││
│           SSH (port 8022)                               ││
└─────────────────────────────────────────────────────────┘
```

---

## Recommended Workflow

1. **Initial Setup:** Use ADB WiFi for quick app installation
2. **Daily Use:** Use SSH (port 8022) for command-line access
3. **Agent Control:** Use Telegram bot on the main Gateway server
4. **Automation:** Write scripts that run on the phone via SSH

---

## Summary Checklist

- [ ] Install Termux from F-Droid
- [ ] Install OpenClaw in Termux
- [ ] Configure and start Gateway
- [ ] Enable Developer Options + USB Debugging
- [ ] Set up WiFi ADB
- [ ] Install Termux API and grant permissions
- [ ] Configure sshd in Termux
- [ ] Create boot script for auto-start
- [ ] Pair Node with Gateway
- [ ] Test camera/sensors

---

*This guide was written based on real-world installation experience. If you encounter other issues, the OpenClaw Discord community and official documentation are excellent resources.*

---

*What challenges have you faced when setting up AI agents on mobile devices?*