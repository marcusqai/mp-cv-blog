---
title: "Fixing OpenClaw Weixin Plugin After Reinstallation: A Step-by-Step Guide"
date: 2026-05-08T14:00:00+08:00
draft: false
tags:
  - tech
  - openclaw
  - weixin
  - server
  - plugin-troubleshooting
summary: "A practical guide to fixing the @tencent-weixin/openclaw-weixin plugin after configuration reset — covering version conflicts, missing install records, and peer dependency symlinks."
description: "After reinstalling the Weixin plugin configuration on OpenClaw, the plugin stopped loading. This post walks through the three root causes and the six-step fix that restored full functionality."
---

After reinstalling the Weixin plugin configuration on my OpenClaw instance, I hit a wall — the plugin refused to load. The Gateway started with only three plugins, and Weixin was completely invisible.

This is a practical walkthrough of how I diagnosed and fixed the issue. If you are facing the same problem, this guide should save you a few hours of digging.

## Environment

| Component | Version |
|---|---|
| **OpenClaw** | 2026.5.4 (325df3e) |
| **Weixin Plugin** | @tencent-weixin/openclaw-weixin@2.4.2 |
| **Node.js** | v22.22.0 |
| **OS** | Linux 6.8.12-18-pve (Proxmox VE) |

## The Problem

After resetting and reinstalling the Weixin plugin configuration:

- The Gateway started with only three plugins — Weixin was not among them.
- Running `openclaw plugins list` showed no trace of the Weixin plugin.
- No errors appeared in the console at startup; the plugin was simply silent.

## Root Cause Analysis

Three independent issues combined to produce this failure.

### 1. Plugin Version Conflict

I had two copies of the Weixin plugin installed in different locations:

| Location | Version |
|---|---|
| `~/.openclaw/extensions/openclaw-weixin/` | v2.4.1 |
| `~/.openclaw/npm/node_modules/@tencent-weixin/openclaw-weixin/` | v2.4.2 |

The newer npm version overrode the extensions version during plugin discovery, but the override created a broken module resolution that prevented the plugin from initializing at all.

### 2. Missing `plugins.installs` Record

This was the most surprising finding. The `openclaw.json` configuration has three separate plugin-related sections:

- **`plugins.allow`** — the allowlist of plugin IDs.
- **`plugins.entries`** — per-plugin settings (e.g., `enabled: true`).
- **`plugins.installs`** — the installation record that tells the Gateway where the plugin code lives.

Even with `plugins.entries.openclaw-weixin.enabled = true` and `"openclaw-weixin"` in `plugins.allow`, the Gateway will **not load the plugin** if there is no corresponding entry in `plugins.installs`. Without this record, the Gateway has no idea where to find the plugin's code on disk.

### 3. Missing Peer Dependency Symlink

The Weixin plugin imports `openclaw/plugin-sdk` at startup. When installed via `npm install` (rather than `openclaw plugins install`), the plugin's `node_modules` does not automatically get a symlink to the host OpenClaw installation.

Result: `require("openclaw/plugin-sdk")` fails silently during module resolution, and the plugin never registers.

## The Fix: Six Steps

### Step 1: Restore Configuration from Backup

If you have a working backup, restore `openclaw.json` from it first. This gives you the correct baseline configuration including channel settings and account data.

```bash
# Example: restore from a tarball backup
tar xzf ~/backups/OpenClaw_Backup_YYYY-MM-DD_HHMM.tar.gz -C /tmp/ ./config/openclaw.json
cp /tmp/config/openclaw.json ~/.openclaw/openclaw.json
```

Verify your account data is intact:

```bash
cat ~/.openclaw/openclaw-weixin/accounts.json
```

### Step 2: Remove Conflicting Plugin Versions

Clean out all existing installations to start fresh:

```bash
echo "y" | openclaw plugins uninstall "@tencent-weixin/openclaw-weixin"
```

Also remove any leftover extensions directory:

```bash
rm -rf ~/.openclaw/extensions/openclaw-weixin
```

### Step 3: Install via npm

Install the plugin directly into the OpenClaw npm directory:

```bash
cd ~/.openclaw/npm
npm install "@tencent-weixin/openclaw-weixin@2.4.2"
```

### Step 4: Create Peer Dependency Symlink

This step is critical. The plugin needs access to OpenClaw's `plugin-sdk` module:

```bash
mkdir -p ~/.openclaw/npm/node_modules/@tencent-weixin/openclaw-weixin/node_modules
ln -sf /usr/lib/node_modules/openclaw \
  ~/.openclaw/npm/node_modules/@tencent-weixin/openclaw-weixin/node_modules/openclaw
```

Verify the symlink works:

```bash
cd ~/.openclaw/npm/node_modules/@tencent-weixin/openclaw-weixin
node -e "require('openclaw/plugin-sdk'); console.log('OK')"
```

### Step 5: Fix openclaw.json

Add the missing `plugins.installs` entry. This is the key step that makes the Gateway actually load the plugin:

```python
import json

with open("~/.openclaw/openclaw.json") as f:
    config = json.load(f)

config["plugins"]["installs"] = {
    "openclaw-weixin": {
        "source": "npm",
        "spec": "@tencent-weixin/openclaw-weixin@2.4.2",
        "installPath": "/root/.openclaw/npm/node_modules/@tencent-weixin/openclaw-weixin",
        "version": "2.4.2",
        "resolvedName": "@tencent-weixin/openclaw-weixin",
        "resolvedVersion": "2.4.2",
        "resolvedSpec": "@tencent-weixin/openclaw-weixin@2.4.2"
    }
}

# Also ensure the channel, allow list, and entry are present
config.setdefault("channels", {})["openclaw-weixin"] = {}
if "openclaw-weixin" not in config["plugins"]["allow"]:
    config["plugins"]["allow"].append("openclaw-weixin")
config["plugins"]["entries"]["openclaw-weixin"] = {"enabled": True}

with open("~/.openclaw/openclaw.json", "w") as f:
    json.dump(config, f, indent=2)
```

### Step 6: Restart the Gateway

```bash
systemctl --user restart openclaw-gateway
```

After restart, verify:

```bash
openclaw plugins list
```

You should see `openclaw-weixin` in the list with status **OK**.

## Key Takeaways

1. **`plugins.installs` is mandatory.** Without it, the Gateway does not know where to find the plugin code — even if everything else is configured correctly.

2. **npm install does not create peer dependency symlinks.** When installing plugins outside of `openclaw plugins install`, you must manually link the host OpenClaw package into the plugin's `node_modules`.

3. **Version conflicts are silent.** Having the same plugin installed in both `extensions/` and `npm/node_modules/` can cause the plugin to vanish without any error message.

4. **Backups are essential.** A recent `openclaw.json` backup saved hours of manual reconstruction.

## Related Issues

This issue may overlap with a broader compatibility problem between OpenClaw 2026.5.x and the Weixin plugin's module loading behavior. See [Tencent/openclaw-weixin #121](https://github.com/Tencent/openclaw-weixin/issues/121) for details on the runtime initialization timeout that some users experience even after successful plugin registration.

---

*Have you run into similar plugin loading issues with OpenClaw? What worked for you?*
