---
title: "Securing Shared Dashboards with Cloudflare Workers"
date: 2026-06-16T13:38:00+08:00
draft: false
tags:
  - tech
  - cloudflare
  - security
  - automation
summary: "Password-protect dashboards with Workers"
description: "A practical guide to securing shared dashboards with Cloudflare Workers, including password gates, CAPTCHA verification, country filtering, and rate limiting."
---

## The Problem: Sharing Dashboards Securely

Teams often need to share operational dashboards with external stakeholders — suppliers, partners, or contractors. The challenge is balancing accessibility with security. You want stakeholders to view the data, but not leave it completely open to the internet.

Most collaboration platforms offer basic link sharing, but lack fine-grained access control. What if you need password protection, geographic restrictions, or brute-force prevention?

This is where Cloudflare Workers come in.

## The Architecture

The solution uses a simple three-layer approach:

```
User → Cloudflare Worker (Security Gate) → Dashboard (Feishu/Notion/Google Sheets)
```

The Worker acts as a reverse proxy with authentication logic. It sits between the user and the dashboard, enforcing security policies before granting access.

### Why Cloudflare Workers?

| Feature | Benefit |
|---------|---------|
| **Edge computing** | Runs on 300+ global edge locations, sub-50ms latency |
| **Request metadata** | Access to CF-IPCountry, CF-Connecting-IP, and other headers |
| **Cookie support** | Set and read cookies for session management |
| **Turnstile integration** | Built-in CAPTCHA without third-party dependencies |
| **Free tier** | 100,000 requests/day, more than enough for most dashboards |

## Implementation: The Password Gate

### Step 1: Basic Password Verification

The core logic is straightforward: check for a valid cookie, verify the password, then redirect.

```javascript
const DASHBOARD_URL = "https://your-dashboard-url.com";
const COOKIE_MAX_AGE = 604800; // 7 days

export default {
  async fetch(request) {
    const url = new URL(request.url);
    
    // Check existing session
    const cookies = parseCookies(request.headers.get("Cookie"));
    if (cookies.session === "verified") {
      return Response.redirect(DASHBOARD_URL, 302);
    }
    
    // Verify password submission
    if (url.searchParams.get("pwd") === PASSWORD) {
      return new Response(null, {
        status: 302,
        headers: {
          "Location": DASHBOARD_URL,
          "Set-Cookie": "session=verified; Path=/; Max-Age=" + COOKIE_MAX_AGE + "; Secure; HttpOnly; SameSite=Lax"
        }
      });
    }
    
    // Show login page
    return new Response(loginPage(), {
      headers: { "Content-Type": "text/html" }
    });
  }
};
```

**Key technical detail:** `Response.redirect()` returns an immutable response. You cannot add headers to it. Instead, create a new `Response` with status 302 and set the `Location` header manually.

### Step 2: Country Whitelisting

Cloudflare provides the visitor country via the `CF-IPCountry` header. This enables geographic restrictions without any additional infrastructure.

```javascript
const ALLOWED_COUNTRIES = ["CN", "HK", "TH"];

// At the start of fetch handler:
const country = request.cf?.country || "";
if (!ALLOWED_COUNTRIES.includes(country)) {
  return new Response(forbiddenPage(country), {
    status: 403,
    headers: { "Content-Type": "text/html" }
  });
}
```

This is particularly useful for B2B scenarios where your stakeholders are in specific regions. It blocks random internet traffic while allowing legitimate users.

### Step 3: CAPTCHA with Turnstile

Cloudflare Turnstile provides bot protection without the friction of traditional CAPTCHAs. It runs invisibly in the background, only challenging suspicious traffic.

```html
<script src="https://challenges.cloudflare.com/turnstile/v0/api.js"></script>
<form method="post">
  <input type="password" name="pwd" placeholder="Password">
  <div class="cf-turnstile" data-sitekey="YOUR_SITE_KEY"></div>
  <button type="submit">Access</button>
</form>
```

Server-side verification:

```javascript
async function verifyTurnstile(token, ip) {
  const response = await fetch(
    "https://challenges.cloudflare.com/turnstile/v0/siteverify",
    {
      method: "POST",
      body: new URLSearchParams({
        secret: TURNSTILE_SECRET_KEY,
        response: token,
        remoteip: ip
      })
    }
  );
  const data = await response.json();
  return data.success === true;
}
```

### Step 4: Rate Limiting

For brute-force protection, Cloudflare offers rate limiting rules in the dashboard. Configure it under **Security → WAF → Rate limiting**:

| Setting | Value |
|---------|-------|
| Field | http.request.uri.path |
| Operator | equals |
| Value | / |
| Rate | 10 requests per 1 hour |
| Action | Block |

This blocks any IP that attempts more than 10 password submissions per hour. Combined with Turnstile, it makes brute-force attacks practically impossible.

## The Complete Security Stack

| Layer | Protection | Implementation |
|-------|-----------|----------------|
| **Geographic** | Block non-whitelisted countries | CF-IPCountry header check |
| **Bot detection** | Prevent automated attacks | Cloudflare Turnstile |
| **Authentication** | Password verification | Cookie-based session |
| **Rate limiting** | Prevent brute-force | Cloudflare WAF rules |
| **Transport** | Encrypted communication | HTTPS (automatic) |

## Lessons Learned

### 1. Cookie Security Matters

Always use `Secure; HttpOnly; SameSite=Lax` flags:
- `Secure` — cookie only sent over HTTPS
- `HttpOnly` — inaccessible to JavaScript (prevents XSS theft)
- `SameSite=Lax` — prevents CSRF attacks

### 2. Session Management

A 7-day cookie balance is practical for dashboards. Users will not be repeatedly challenged, but compromised sessions expire relatively quickly.

### 3. Error Messages

Do not reveal whether the password is wrong or the CAPTCHA failed. Generic error messages prevent attackers from enumerating valid passwords.

### 4. Country Detection

The `CF-IPCountry` header is highly reliable but not perfect. VPN users may appear from different countries. For dashboard sharing with known stakeholders, this is usually acceptable.

## When to Use This Pattern

This approach works well when:
- You need to share dashboards with external stakeholders
- The dashboard platform lacks built-in password protection
- You need geographic restrictions
- You want enterprise-grade security without enterprise costs

It works with any dashboard that supports link sharing — Feishu Bitable, Notion, Google Sheets, Grafana, or custom web applications.

---

*What security challenges have you faced when sharing operational data with external partners?*
