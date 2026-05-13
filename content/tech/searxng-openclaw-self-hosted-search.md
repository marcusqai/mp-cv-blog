---
title: "Self-Hosted Web Search with SearXNG and OpenClaw"
date: 2026-05-13T14:45:00+08:00
draft: false
tags:
  - tech
  - searxng
  - openclaw
  - privacy
  - docker
  - self-hosted
summary: "A complete guide to installing SearXNG on a Synology NAS, fixing common permission issues, enabling the JSON API, and integrating it as a free web search provider in OpenClaw."
description: "Step-by-step tutorial for deploying SearXNG on Synology NAS with Docker, resolving file permission problems, enabling JSON API access, and connecting it to OpenClaw for private, unlimited web search."
---

## The Problem

Most AI assistants rely on commercial web search APIs — Brave Search, Google Custom Search, or similar services. These come with API keys, rate limits, monthly quotas, and sometimes region restrictions. Worse, every query you make passes through their servers.

I wanted something different: **free, unlimited, private web search** that runs entirely on my own network. Enter [SearXNG](https://github.com/searxng/searxng), an open-source metasearch engine that aggregates results from over 249 search services without tracking or profiling users.

This is a complete guide covering installation on a Synology NAS, the permission issues you will almost certainly hit, enabling the JSON API that OpenClaw needs, and the final integration steps.

---

## Prerequisites

- **Synology NAS** running DSM 7.2 or later (with Container Manager)
- **OpenClaw** installed and running on your server
- **SSH access** to your NAS (needed for fixing permissions)
- Basic familiarity with Docker and YAML configuration

---

## Step 1: Install Container Manager

1. Open **Package Center** on your Synology NAS.
2. Search for **Container Manager** (on DSM 7.2+) or **Docker** (older versions).
3. Click **Install**.

---

## Step 2: Create the Data Directory

Open **File Station** and navigate to the `docker` folder. Inside it, create a new folder called `searxng` (lowercase only):

```
/volume1/docker/searxng/
```

This directory will hold your SearXNG configuration files.

---

## Step 3: Install SearXNG via Task Scheduler

### 3.1 Create a Scheduled Task

1. Open **Control Panel** → **Task Scheduler** → **Create** → **Scheduled Task** → **User-defined Script**.

### 3.2 General Tab

| Field | Value |
|:---|:---|
| **Task** | Install SearXNG |
| **Enabled** | Uncheck |
| **User** | **root** |

### 3.3 Schedule Tab

| Field | Value |
|:---|:---|
| **Run on** | Today's date |
| **Frequency** | Do not repeat |

### 3.4 Task Settings Tab

1. Check "Send run details by email" (optional).
2. Paste the following into the **Run command** area:

```bash
docker run -d --name=searxng \
  -p 5147:8080 \
  -v /volume1/docker/searxng:/etc/searxng \
  --restart always \
  searxng/searxng
```

> **Port note:** `5147` is the external port you choose. `8080` is the container's internal port — do not change it.

### 3.5 Run the Task

1. Click **OK** and confirm any warning pop-ups.
2. Enter your DSM password and click **Submit**.
3. In the task list, select **Install SearXNG** and click **Run**.

The installation takes a few seconds to a few minutes depending on your internet speed.

---

## Step 4: Verify Installation

Open your browser and navigate to:

```
http://your-nas-ip:5147/
```

If you see the SearXNG search page, the installation succeeded.

---

## Step 5: Fix the settings.yml Permission Issue

### The Problem

This is the part most guides skip. Docker runs the container as **root**, and the `settings.yml` file it automatically creates is owned by `root:root`. When you try to edit it through File Station as a regular user, you get:

```
Permission denied
```

```
-rw-r--r--  1 root  root  4521  May 13 14:00  settings.yml
                            ↑↑↑↑
                      owned by root, regular users can only read
```

### Solution 1: Change Ownership via SSH (Recommended)

```bash
# SSH into your NAS
ssh your-admin-user@your-nas-ip

# Change file ownership to your user
sudo chown your-user:users /volume1/docker/searxng/settings.yml

# Verify the change
ls -la /volume1/docker/searxng/settings.yml

# Expected output:
# -rw-r--r--  1 your-user  users  4521  May 13 14:00  settings.yml
```

Now you can edit the file through File Station or any editor.

### Solution 2: Edit Directly via SSH

```bash
ssh your-admin-user@your-nas-ip
sudo vi /volume1/docker/searxng/settings.yml
```

### Solution 3: Delete and Recreate (More Tedious)

```bash
ssh your-admin-user@your-nas-ip
sudo -i
rm /volume1/docker/searxng/settings.yml
touch /volume1/docker/searxng/settings.yml
chown your-user:users /volume1/docker/searxng/settings.yml
```

> Note: Deleting the file and restarting the container will regenerate the default settings. Any customizations you made will be lost.

---

## Step 6: Enable the JSON API

### Why This Matters

OpenClaw communicates with SearXNG through its **JSON API endpoint**. But SearXNG **does not enable JSON format by default**. Without this, OpenClaw gets a 403 Forbidden error every time it tries to search.

### 6.1 Edit settings.yml

Open `/volume1/docker/searxng/settings.yml` (using File Station or SSH) and add or modify the following section:

```yaml
search:
  formats:
    - html
    - json
```

> ⚠️ **YAML indentation matters** — use 2 spaces, not tabs.

### 6.2 Enable Additional Search Engines (Optional)

By default, most mainstream search engines are disabled in SearXNG to prevent IP blocking from upstream providers. You will see only a handful of engines active (Wikipedia, arXiv, etc.). To get richer results, you can enable more:

```yaml
engines:
  - name: google
    engine: google
    shortcut: g
    enabled: true
    use_mobile_ui: true
  - name: bing
    engine: bing
    shortcut: b
    enabled: true
  - name: duckduckgo
    engine: duckduckgo
    shortcut: ddg
    enabled: true
```

> ⚠️ Be cautious — enabling too many engines from a single IP may trigger rate limiting or CAPTCHAs from upstream providers.

### 6.3 Disable the Limiter (Dev/Test Only)

For testing or home use, you may want to disable the built-in rate limiter:

```yaml
server:
  limiter: false
```

> ⚠️ In production environments, keep `limiter: true` to avoid getting blocked by upstream search engines.

### 6.4 Restart the Container

```bash
docker restart searxng
```

Or use the Container Manager UI: select the **searxng** container → **Action** → **Restart**.

### 6.5 Verify the JSON API

```bash
curl -s "http://localhost:5147/search?q=test&format=json" | python3 -c "
import sys, json
d = json.load(sys.stdin)
print('Results:', len(d.get('results', [])))
"
```

Expected output:

```
Results: 38
```

If you still get `403 Forbidden`, double-check your `settings.yml` — the `json` format entry is likely missing or has incorrect indentation.

---

## Step 7: Integrate with OpenClaw

### 7.1 Configure openclaw.json

Edit your OpenClaw configuration file and add the following sections:

```json
{
  "env": {
    "SEARXNG_BASE_URL": "http://your-nas-ip:5147"
  },
  "tools": {
    "web": {
      "search": {
        "enabled": true,
        "provider": "searxng"
      }
    }
  },
  "plugins": {
    "allow": [
      "telegram",
      "memory-core",
      "active-memory",
      "openclaw-weixin",
      "device-pair",
      "deepseek",
      "searxng"
    ],
    "entries": {
      "searxng": {
        "enabled": true,
        "config": {
          "webSearch": {
            "baseUrl": "http://your-nas-ip:5147"
          }
        }
      }
    }
  }
}
```

Three things to note:
1. The `SEARXNG_BASE_URL` environment variable enables auto-detection.
2. `tools.web.search.provider` must be set to `"searxng"`.
3. `plugins.allow` must include `"searxng"` — otherwise the plugin will not load even if configured.

### 7.2 Restart OpenClaw Gateway

A hot-reload (SIGUSR1) may not switch the search provider. Perform a full restart:

```bash
systemctl --user restart openclaw-gateway.service
```

Or:

```bash
openclaw gateway restart
```

Wait a few seconds, then verify:

```bash
curl -s localhost:8888/health
```

Expected: `{"ok":true,"status":"live"}`

---

## Step 8: Test

Use the `web_search` tool in OpenClaw:

```
web_search({ query: "latest AI news", count: 5 })
```

Verify that the response shows:

```json
{
  "query": "latest AI news",
  "provider": "searxng",
  "results": [...]
}
```

If it still shows `duckduckgo`, the Gateway restart did not take effect — restart again.

---

## Lessons Learned

### 1. JSON API Is Not Enabled by Default

This is the most common pitfall. SearXNG serves HTML results by default. The JSON API requires manually adding `json` to the `search.formats` list in `settings.yml`.

**Symptom:** `curl /search?q=test&format=json` returns 403 Forbidden.
**Fix:** Add `formats: [html, json]` and restart the container.

### 2. File Ownership Issues Are Inevitable

Docker creates files as `root:root`. Regular NAS users cannot edit them. The cleanest fix is `sudo chown your-user:users <filename>` — not deleting and recreating the file.

### 3. Hot-Reload Does Not Switch Search Providers

OpenClaw's SIGUSR1 hot-reload only partially reloads configuration. Switching the web search provider requires a **full Gateway restart**.

### 4. plugins.allow Must Include searxng

Even with perfect configuration, the SearXNG plugin will not load if `"searxng"` is missing from the `plugins.allow` whitelist.

### 5. Most Search Engines Are Disabled by Default

Out of 249 possible engines, only about 10 are enabled in a fresh SearXNG install. This is intentional — it prevents upstream search engines from blocking your IP. Mainstream engines like Google and Bing are disabled by default. Enable them manually if you need broader coverage.

### 6. Dual Configuration Is Safest

OpenClaw supports two configuration methods for SearXNG:
- The `SEARXNG_BASE_URL` environment variable
- The `tools.web.search.provider` + `plugins.entries.searxng.config` config block

Setting **both** ensures maximum compatibility, especially after Gateway restarts.

---

## What Changed for Me

Before SearXNG, my OpenClaw assistant used DuckDuckGo as the default search provider — free but with inconsistent result quality and no control over which engines were queried.

Now every search goes through my own metasearch instance:

| Aspect | Before (DuckDuckGo) | After (SearXNG) |
|:---|:---|:---|
| **Cost** | Free | Free |
| **Rate Limits** | Unknown | Unlimited |
| **Privacy** | Queries go to third party | Queries stay on local network |
| **Engine Control** | None | Full control — enable/disable 249 engines |
| **Result Quality** | Good | Excellent (aggregates multiple sources) |

The entire setup took about 30 minutes, including debugging the permission issue and JSON API problem that cost me an extra 20 minutes. Worth every minute.

---

*What tools or automations have improved your workflow?*
