---
title: "Cloudflare Tunnel: Expose Your Home Server and Connect Remote Nodes Securely"
date: 2026-05-12T03:00:00+08:00
draft: false
tags:
  - tech
  - cloudflare
  - self-hosted
  - networking
  - openclaw
summary: "How I used Cloudflare Tunnel to expose a home server to the internet without port forwarding, then connected remote nodes through it."
description: "A step-by-step guide to setting up Cloudflare Tunnel on a home server, configuring ingress rules, and connecting remote nodes securely without opening any router ports."
---

## Overview

I run a self-hosted server at home. I also have several remote nodes — a Linux box on a different network and an Android phone running Termux — that need to connect back to the main server.

The old approach? Port forwarding on the router, a dynamic DNS service, and hoping the ISP doesn't change the external IP. It works, but it's fragile and exposes your home network directly to the internet.

**Cloudflare Tunnel changes all of that.** No port forwarding. No exposed IPs. Free HTTPS. And it just works.

Here's how I set it up, including the gotchas I hit along the way.

---

## Why Cloudflare Tunnel?

| Feature | Traditional Port Forwarding | Cloudflare Tunnel |
|:---|:---|:---|
| **Router config** | Required (open ports) | Not needed |
| **External IP exposure** | Yes | No |
| **HTTPS** | Manual (Let's Encrypt) | Automatic |
| **DDoS protection** | None | Built-in (Cloudflare edge) |
| **Dynamic IP handling** | Need DDNS | Not needed |
| **Cost** | Free | Free |

The tunnel creates an outbound-only connection from your server to Cloudflare's edge. No inbound ports. Nothing to forward.

---

## Step 1: Install cloudflared

On the server (Ubuntu/Debian):

```bash
# Download the latest amd64 binary
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64

# Make it executable and move to PATH
chmod +x cloudflared-linux-amd64
sudo mv cloudflared-linux-amd64 /usr/local/bin/cloudflared

# Verify installation
cloudflared --version
```

You should see something like `cloudflared version 2026.x.x`.

---

## Step 2: Create a Tunnel

### Via Cloudflare Zero Trust Dashboard

1. Go to the Cloudflare Zero Trust dashboard
2. Navigate to **Networks** → **Tunnels**
3. Click **Create a tunnel**
4. Choose **Cloudflared** as the connector type
5. Give it a name (e.g., `home-server`)
6. Copy the **install command** — it contains your tunnel token

### Start the Tunnel

The command looks like this:

```bash
cloudflared tunnel --no-autoupdate run --token YOUR_TUNNEL_TOKEN
```

Once you confirm it connects (you'll see `Connected` and a list of edge nodes), stop it and set it up as a systemd service.

---

## Step 3: Create a systemd Service

Create the service file at `/etc/systemd/system/cloudflared.service`:

```ini
[Unit]
Description=Cloudflare Tunnel
After=network-online.target
Wants=network-online.target

[Service]
Type=notify
ExecStart=/usr/local/bin/cloudflared tunnel --no-autoupdate run --token YOUR_TUNNEL_TOKEN
Restart=always
RestartSec=5
StartLimitBurst=5

[Install]
WantedBy=multi-user.target
```

Replace `YOUR_TUNNEL_TOKEN` with the actual token from the dashboard.

Then enable and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable cloudflared
sudo systemctl start cloudflared
sudo systemctl status cloudflared
```

You should see `active (running)` with a green indicator.

---

## Step 4: Configure Your Domain

### Add a DNS Record

In the Cloudflare Dashboard DNS settings:

1. Create a **CNAME** record
2. **Name**: your service name (e.g., `openclaw`, `nextcloud`)
3. **Target**: `YOUR-TUNNEL-ID.cfargotunnel.com`
4. **Proxy status**: Proxied (orange cloud ON)

Or, do it directly in the Zero Trust Dashboard under **Networks** → **Tunnels** → **Public Hostnames**.

### Add Public Hostnames

For each service you want to expose, add a public hostname:

| Field | Value |
|:---|:---|
| **Subdomain** | your service name |
| **Domain** | your domain |
| **Path** | (leave blank for full access) |
| **Service Type** | `HTTP` |
| **URL** | `localhost:PORT` |

For example, to expose a web interface running on port 8888:

| Field | Value |
|:---|:---|
| **URL** | `localhost:8888` |

---

## Step 5: Understand the Ingress Rules

Cloudflare Tunnel uses **ingress rules** to route traffic from your domain to local services. Rules are evaluated in order — the first match wins.

### Key Insight: Path Matters

Different services use different URL paths:

| Service | Path Used | Ingress Rule |
|:---|:---|:---|
| Web UI with base path | `/dashboard` | `/dashboard/*` → `localhost:8888` |
| Node WebSocket connection | `/` (root) | `/*` → `localhost:8888` |
| Health checks | `/healthz` | `/*` → `localhost:8888` |

**Critical:** If your nodes connect via WebSocket to the root path, you **must** have a catch-all rule (`/*`). A rule that only covers `/dashboard` will reject node connections with a 404.

### Example Ingress Configuration

```yaml
ingress:
  - hostname: openclaw.your-domain.tld
    service: http://localhost:8888
  - service: http_status:404
```

This catches all traffic to the hostname and routes it to the local service. The 404 fallback prevents undefined routes from leaking.

---

## Step 6: Configure the Server Application

Your server application needs to know it's being accessed from an external domain. Two settings matter:

### Base Path (if applicable)

If your app uses a base path (like `/dashboard`), configure it:

```json
{
  "gateway": {
    "controlUi": {
      "basePath": "/dashboard"
    }
  }
}
```

### Allowed Origins

For WebSocket connections to work through the tunnel, add your domain to allowed origins:

```json
{
  "gateway": {
    "controlUi": {
      "allowedOrigins": ["https://openclaw.your-domain.tld"]
    }
  }
}
```

Restart the application after making these changes.

---

## Step 7: Connect Remote Nodes

This is where it gets interesting. Your remote nodes (other machines, phones, whatever) need to connect back to the server — but now through the tunnel instead of a LAN IP.

### Before Tunnel (LAN)

```json
{
  "gateway": {
    "host": "192.168.30.1",
    "port": 8888,
    "tls": false
  }
}
```

### After Tunnel (Cloudflare)

```json
{
  "gateway": {
    "host": "openclaw.your-domain.tld",
    "port": 443,
    "tls": true
  }
}
```

Or using the URL format:

```json
{
  "gateway": {
    "url": "wss://openclaw.your-domain.tld"
  }
}
```

### Connecting a Linux Node

On the remote Linux machine:

1. Stop the existing node process
2. Update the configuration to point to the tunnel domain
3. Restart the node

```bash
# Stop old connection
pkill -f 'node run'

# Update config
python3 << 'EOF'
import json

config_path = '/path/to/your/config.json'
with open(config_path) as f:
    config = json.load(f)

config['gateway'] = {
    'mode': 'remote',
    'remote': {
        'url': 'wss://openclaw.your-domain.tld',
        'password': 'your-gateway-password'
    }
}

with open(config_path, 'w') as f:
    json.dump(config, f, indent=2)
EOF

# Restart
nohup openclaw node run > /dev/null 2>&1 &
```

### Connecting an Android Node (Termux)

Android/Termux nodes have a quirk: the configuration file gets overwritten on startup. You **must** use command-line arguments:

```bash
# Stop existing node
pkill -f 'openclaw-node'

# Start with explicit tunnel parameters
cd ~/.openclaw && \
OPENCLAW_GATEWAY_PASSWORD=your-gateway-password \
nohup openclaw node run \
  --host openclaw.your-domain.tld \
  --port 443 \
  --tls \
  > node.log 2>&1 &
```

**Why command-line?** The node config file on Android gets reset by the application on each start. CLI arguments override it reliably.

---

## Step 8: Verify the Connection

On the server, check node status:

```bash
openclaw nodes status
```

You should see all nodes listed as `connected` with recent timestamps.

---

## Security Considerations

### Is This Safe?

Yes, with caveats. Here's my risk assessment:

| Risk | Level | Mitigation |
|:---|:---|:---|
| Full gateway exposed to internet | Medium | Gateway requires authentication (password/token) |
| Unauthorized access | Low | Authentication blocks unauthenticated requests |
| DDoS attack | Low | Cloudflare edge absorbs and filters traffic |
| Scanner/bot traffic | Medium | Visible in logs, but authentication blocks access |
| Application vulnerability | Low | Must pass authentication first |

### Existing Protections

- **Gateway authentication** — password or token required for all connections
- **Loopback binding** — server only listens on localhost, not exposed directly
- **Cloudflare TLS** — all traffic encrypted in transit
- **Cloudflare DDoS protection** — automatic at the edge
- **Allowed Origins** — WebSocket connections restricted to your domain

### Optional Hardening

1. **Cloudflare Access** — require Zero Trust authentication before reaching the tunnel (free tier available)
2. **Rate limiting** — configure in Cloudflare dashboard
3. **WAF rules** — enable Cloudflare's Web Application Firewall
4. **Regular updates** — keep cloudflared and your application updated

---

## Troubleshooting

### Node Won't Connect — 404 Error

**Symptom:** Node logs show `HTTP 404` when trying to connect.

**Cause:** Ingress rule doesn't cover the root path `/`.

**Fix:** Add a catch-all rule (`/*`) or ensure your ingress rule covers the path the node uses for WebSocket connections.

### Node Keeps Reconnecting

**Symptom:** Node shows `connected` then `disconnected` in a loop.

**Cause:** Password mismatch or TLS configuration error.

**Fix:** Verify the gateway password matches exactly. On Android, use CLI arguments (`--tls`) instead of relying on config file settings.

### Tunnel Connects But Service Returns Error

**Symptom:** Tunnel shows `Connected` in logs, but browser shows 502 Bad Gateway.

**Cause:** The local service isn't running, or the port in the ingress rule is wrong.

**Fix:** Verify the service is running on the expected port.

### cloudflared Won't Start as systemd Service

**Symptom:** `systemctl status cloudflared` shows `failed`.

**Cause:** Token is invalid, or network isn't ready yet.

**Fix:** Test manually first. If it works manually, check the systemd service file for typos.

---

## Port Summary

| Port | Purpose |
|:---:|:---|
| **443** | Cloudflare Tunnel (HTTPS, outbound only) |
| **8888** | Local gateway service (loopback only) |
| **80/8443** | Not needed with Cloudflare Tunnel |

**Nothing is forwarded on your router.** Zero open ports. The tunnel is an outbound-only connection.

---

## What I Learned

1. **Cloudflare Tunnel eliminates port forwarding entirely.** Your home server is accessible from anywhere without touching router settings.

2. **Ingress rules are path-sensitive.** A rule for `/dashboard` doesn't cover `/`. If your app uses multiple paths (Web UI + WebSocket API), you need rules that cover all of them.

3. **Android/Termux nodes need CLI arguments.** Config files get overwritten. Command-line flags are the only reliable way to set connection parameters.

4. **Authentication is your first line of defense.** Even with the entire application exposed through the tunnel, unauthenticated requests are rejected at the gateway level.

5. **systemd with RestartSec handles transient failures.** If the network drops, cloudflared reconnects automatically within seconds.

---

*What's your go-to solution for exposing home services to the internet?*
