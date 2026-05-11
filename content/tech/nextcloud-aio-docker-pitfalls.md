---
title: "Nextcloud AIO Docker: 5 Pitfalls I Hit During Setup (and How to Fix Them)"
date: 2026-05-12T02:47:00+08:00
draft: false
tags:
  - tech
  - docker
  - nextcloud
  - self-hosted
summary: "A detailed walkthrough of every error I hit deploying Nextcloud AIO with Docker, and the exact fixes for each one."
description: "Installing Nextcloud AIO with Docker sounds straightforward until it isn't. Here are five real issues I encountered — Docker API version mismatch, Portainer volume prefix bugs, port conflicts, Cloudflare Tunnel misconfiguration, and named volume naming traps — with exact commands and solutions."
---

## Overview

Nextcloud All-in-One (AIO) is supposed to be the easiest way to self-host Nextcloud. One container, automatic certificate handling, built-in backup — sounds simple, right?

It wasn't.

Over the past few hours, I hit **five different issues** that either had poor documentation or no clear fix online. Here's every one of them, with the exact commands that finally worked.

---

## 1. Docker API Version Mismatch

**The Error:**

```
DOCKER_API_VERSION was found to be set to '1.45'.
Please note that only v1.44 is officially supported and tested
by the maintainers of Nextcloud AIO.
```

**What Happened:**

My Docker server runs API version 1.45, but Nextcloud AIO is **only tested against 1.44**. Newer versions may work, but you're on your own — and things can break without warning.

**The Fix:**

Add the environment variable to force the AIO container to use API 1.44:

```bash
--env DOCKER_API_VERSION=1.44
```

**Note:** If your server runs 1.43 (older Docker), try it first. Some features might not work, but it's worth testing before upgrading.

---

## 2. Portainer Automatically Prefixes Volume Names

**The Error:**

```
It seems like you did not give the mastercontainer volume the correct name?
(The 'nextcloud_aio_mastercontainer' volume was not found.)
Using a different name is not supported since the built-in backup
solution will not work in that case!
```

**What Happened:**

Portainer Stacks automatically prepend volume names with the stack name prefix. When I defined the volume as `nextcloud_aio_mastercontainer` in a Portainer Stack called `nextcloud-aio`, Docker actually created:

```
nextcloud-aio_nextcloud_aio_mastercontainer
```

Nextcloud AIO checks for the **exact** volume name `nextcloud_aio_mastercontainer` on startup. The prefixed name fails this check, and the container refuses to start.

**The Fix:**

Skip Portainer Stack for the mastercontainer. Use the Docker CLI directly:

```bash
# Step 1: Create the correct named volume
docker volume create nextcloud_aio_mastercontainer

# Step 2: Run the container with CLI (no Portainer)
docker run   --init   --sig-proxy=false   --name nextcloud-aio-mastercontainer   --restart always   -d   --publish 20080:80   --publish 28080:8080   --publish 28443:8443   --env DOCKER_API_VERSION=1.44   --volume nextcloud_aio_mastercontainer:/mnt/docker-aio-config   --volume /var/run/docker.sock:/var/run/docker.sock:ro   ghcr.io/nextcloud-releases/all-in-one:latest
```

**Key insight:** The `--restart always` flag means you only run this **once**. Docker will auto-start the container on every reboot.

---

## 3. Port 443 Already in Use

**The Error:**

```
Domaincheck container is not running.
This is not expected. Most likely this happened because
port 443 is already in use on your server.
```

**What Happened:**

Nextcloud AIO tries to bind port 443 for automatic HTTPS certificate provisioning via Let's Encrypt. If another service (nginx, Apache, or any reverse proxy) already uses port 443, the domaincheck container fails to start.

**The Fix:**

Tell AIO to use a different port for its internal Apache server:

```bash
--env APACHE_PORT=2443
```

This moves the internal Apache HTTPS listener from 443 to 2443. You can then point a reverse proxy (like Cloudflare Tunnel or nginx) at port 2443.

---

## 4. Cloudflare Tunnel Pointing to the Wrong Port

**The Symptom:**

```
Domain does not point to this server or the reverse proxy
is not configured correctly.
```

**What Happened:**

Nextcloud AIO uses **two different ports** for two different things:

| Port | Purpose |
|:---:|:---|
| **28080** | AIO Management Interface (initial setup only) |
| **2443** | Nextcloud actual service (Apache, set by `APACHE_PORT`) |

I initially pointed the Cloudflare Tunnel to port 28080, which is the management UI. But the domain check tries to reach the actual Nextcloud service, which lives on port 2443.

**The Fix:**

In Cloudflare Zero Trust Dashboard → Networks → Tunnels → Public Hostname:

| Field | Value |
|:---|:---|
| **Subdomain** | `nextcloud` |
| **Domain** | `your-domain.tld` |
| **Service Type** | `HTTP` |
| **URL** | `localhost:2443` |

Not 28080. Not 443. **2443.**

---

## 5. Named Volume vs Bind Mount for Mastercontainer

**The Error:**

```
The 'nextcloud_aio_mastercontainer' volume was not found.
```

**What Happened:**

I tried to use a bind mount for the mastercontainer volume:

```bash
--volume /some/local/path:/mnt/docker-aio-config
```

This creates a valid Docker volume mapping, but Nextcloud AIO's internal backup system **requires** a Docker named volume with the exact name `nextcloud_aio_mastercontainer`. Bind mounts bypass Docker's volume management entirely, so the backup system can't find the data.

**The Fix:**

Always use a **named volume** for the mastercontainer:

```bash
docker volume create nextcloud_aio_mastercontainer

--volume nextcloud_aio_mastercontainer:/mnt/docker-aio-config
```

If you want your data in a specific path on the host, configure the backup directory **inside** the AIO management interface after setup, not through Docker volume mapping.

---

## The Final Working Command

Combining all five fixes, here's the complete, tested command:

```bash
docker run   --init   --sig-proxy=false   --name nextcloud-aio-mastercontainer   --restart always   -d   --publish 20080:80   --publish 28080:8080   --publish 28443:8443   --env DOCKER_API_VERSION=1.44   --env APACHE_PORT=2443   --volume nextcloud_aio_mastercontainer:/mnt/docker-aio-config   --volume /var/run/docker.sock:/var/run/docker.sock:ro   ghcr.io/nextcloud-releases/all-in-one:latest
```

**Port summary:**

| Port | Purpose |
|:---:|:---|
| **20080** | Nextcloud web interface (HTTP) |
| **28080** | AIO Management Interface (setup) |
| **28443** | AIO Management Interface (HTTPS) |
| **2443** | Internal Apache (HTTPS, for reverse proxy) |

---

## Quick Reference: Error → Fix

| Error | Root Cause | Fix |
|:---|:---|:---|
| `only v1.44 is officially supported` | Docker API version mismatch | `--env DOCKER_API_VERSION=1.44` |
| `mastercontainer volume not found` | Portainer adds volume prefix | Use CLI, not Portainer Stack |
| `port 443 already in use` | Another service binds 443 | `--env APACHE_PORT=2443` |
| `Domain does not point to this server` | Tunnel points to wrong port | Point Tunnel to `:2443`, not `:28080` |
| `built-in backup will not work` | Bind mount instead of named volume | Use `docker volume create` + named volume |

---

*What self-hosting rabbit holes have you fallen into lately?*
