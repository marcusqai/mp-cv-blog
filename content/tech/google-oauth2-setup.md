# How to Set Up Google OAuth 2.0 for Company Internal Tools — Full Guide with Real Problems & Fixes

## 🔐 Introduction

Google OAuth 2.0 allows your internal tools to access Google Workspace services (Gmail, Calendar, Drive, etc.) on behalf of company users. This guide covers the complete setup process, from creating a Google Cloud project to implementing the OAuth flow in your application, including all the real problems we encountered and their solutions.

**Use Cases for Internal Tools:**
- 📧 Gmail management and automation
- 📅 Google Calendar sync for internal scheduling tools
- 📁 Google Drive file management
- 📸 Google Photos access for company assets
- 👥 Google Contacts/People API integration
- ✅ Google Tasks integration

---

## Part 1: Create a Google Cloud Project

### Step 1: Access Google Cloud Console

1. Go to https://console.cloud.google.com/
2. Sign in with your company Google account
3. Click "Select a project" → "New Project"
4. Name your project (e.g., "Company Internal Tools")
5. Select your organization if using Google Workspace
6. Click "Create"

### Step 2: Enable Required APIs

For each Google service you need, enable the API:

1. Go to "APIs & Services" → "Library"
2. Search for and enable:
   - **Gmail API** — for email read/send
   - **Google Calendar API** — for calendar events
   - **Google Drive API** — for file management
   - **Google Photos Library API** — for photos
   - **People API** — for contacts
   - **Tasks API** — for task management

---

## Part 2: Configure OAuth Consent Screen

### Step 1: Navigate to OAuth Consent

1. Go to "APIs & Services" → "OAuth consent screen"
2. Choose "Internal" (for Google Workspace) or "External" (for personal accounts)
3. Click "Create"

### Step 2: Fill in App Information

- **App name:** Your company tool name
- **User support email:** support@yourcompany.com
- **Developer contact:** same email
- **App logo:** optional (recommended for production)

### Step 3: Add Scopes

Add only the scopes your app needs:

```
# Email reading only
https://www.googleapis.com/auth/gmail.readonly

# Email reading + sending
https://www.googleapis.com/auth/gmail.readonly
https://www.googleapis.com/auth/gmail.send

# Full Gmail access
https://www.googleapis.com/auth/gmail.modify

# Calendar
https://www.googleapis.com/auth/calendar
https://www.googleapis.com/auth/calendar.events

# Drive
https://www.googleapis.com/auth/drive
https://www.googleapis.com/auth/drive.file

# Photos
https://www.googleapis.com/auth/photoslibrary.readonly

# Contacts
https://www.googleapis.com/auth/contacts.readonly

# Tasks
https://www.googleapis.com/auth/tasks
```

### Step 4: Add Test Users (Critical for Unverified Apps!)

If your app is not yet verified by Google, you MUST add test users:

1. Scroll to "Test users"
2. Click "Add Users"
3. Add all company email addresses that will use the app
4. These users can approve access without Google verification

> ⚠️ **Important:** Without test users, regular users will see "This app isn't verified" and be blocked.

---

## Part 3: Create OAuth 2.0 Credentials

### Step 1: Create Credentials

1. Go to "APIs & Services" → "Credentials"
2. Click "Create Credentials" → "OAuth client ID"
3. Application type: **Web application**
4. Name: "Company Internal Tool"

### Step 2: Configure Authorized Redirect URIs

Add your callback URL:
```
http://localhost:8889/callback
```
OR for public access:
```
http://your-public-domain.com/oauth2callback
```

### Step 3: Save Credentials

After creating, you'll see:
- **Client ID**
- **Client Secret**

Save these securely — you'll need them in your application.

---

## Part 4: Implement OAuth Flow in Your Application

### Node.js Complete Implementation (Recommended)


This is a complete, working OAuth server with HTML landing page:

```javascript
const http = require('http');
const url = require('url');
const fs = require('fs');
const { google } = require('googleapis');

// ── Credentials ────────────────────────────────────────────────
const CLIENT_ID = 'YOUR_CLIENT_ID.apps.googleusercontent.com';
const CLIENT_SECRET = 'YOUR_CLIENT_SECRET';
const REDIRECT_URL = 'http://your-public-domain.com:8899/callback';
const PORT = 8899;
const TOKEN_FILE = './credentials/google-tokens.json';

// ── Scopes ─────────────────────────────────────────────────────
const SCOPES = [
  'https://www.googleapis.com/auth/gmail.readonly',
  'https://www.googleapis.com/auth/gmail.send',
  'https://www.googleapis.com/auth/calendar',
  'https://www.googleapis.com/auth/drive',
  'https://www.googleapis.com/auth/tasks'
];

const oauth2Client = new google.auth.OAuth2(CLIENT_ID, CLIENT_SECRET, REDIRECT_URL);
const authUrl = oauth2Client.generateAuthUrl({
  access_type: 'offline',
  scope: SCOPES,
  prompt: 'consent'
});

// ── HTML Landing Page ───────────────────────────────────────────
const HTML_PAGE = `<!DOCTYPE html>
<html>
<head>
<title>Google OAuth</title>
<style>
  body { font-family: sans-serif; max-width: 560px; margin: 80px auto; padding: 20px; text-align: center; }
  .card { background: white; border-radius: 12px; padding: 40px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); }
  h1 { color: #1a73e8; }
  p { color: #666; margin-bottom: 30px; }
  .btn { display: inline-block; background: #1a73e8; color: white; padding: 14px 32px; border-radius: 8px;
         text-decoration: none; font-size: 16px; }
  .btn:hover { background: #1557b0; }
  .note { margin-top: 16px; font-size: 13px; color: #999; }
</style>
</head>
<body><div class="card">
  <h1>🔐 Google OAuth</h1>
  <p>Click below to authorize access to Google services</p>
  <a href="${authUrl}" class="btn">Login with Google</a>
  <div class="note">Scopes: Gmail, Calendar, Drive, Tasks</div>
</div></body></html>`;

// ── Server ──────────────────────────────────────────────────────
const server = http.createServer((req, res) => {
  const parsed = url.parse(req.url, true);

  if (parsed.pathname === '/callback') {
    const code = parsed.query.code;
    if (code) {
      res.writeHead(200, { 'Content-Type': 'text/html' });
      res.end('<html><body style="text-align:center;padding:60px;font-family:sans-serif">'
        + '<h1 style="color:#34a853">✅ Done!</h1>'
        + '<p>Authorization complete. You can close this window.</p></body></html>');
      oauth2Client.getToken(code).then(({ tokens }) => {
        fs.mkdirSync('./credentials', { recursive: true });
        fs.writeFileSync(TOKEN_FILE, JSON.stringify(tokens, null, 2));
        console.log('[SUCCESS] Tokens saved to', TOKEN_FILE);
        process.exit(0);
      }).catch(e => { console.error('[ERROR]', e.message); process.exit(1); });
    }
  } else {
    res.writeHead(200, { 'Content-Type': 'text/html' });
    res.end(HTML_PAGE);
  }
});

server.listen(PORT, '0.0.0.0', () => {
  console.log('[READY] http://0.0.0.0:' + PORT + '/  — open in browser');
});
```

**Run:**
```bash
node oauth-server.js
# Then open http://your-public-domain.com:8899/ in browser
```

### Option B: Python Implementation

```python
import flask
from google_auth_oauthlib.flow import Flow

app = flask.Flask(__name__)

CLIENT_ID = 'YOUR_CLIENT_ID.apps.googleusercontent.com'
CLIENT_SECRET = 'YOUR_CLIENT_SECRET'
REDIRECT_URL = 'http://localhost:8889/callback'

SCOPES = [
    'https://www.googleapis.com/auth/gmail.readonly',
    'https://www.googleapis.com/auth.gmail.send',
    'https://www.googleapis.com/auth/calendar'
]

@app.route('/')
def index():
    flow = Flow.from_client_config(
        {
            "web": {
                "client_id": CLIENT_ID,
                "client_secret": CLIENT_SECRET,
                "auth_uri": "https://accounts.google.com/o/oauth2/auth",
                "token_uri": "https://oauth2.googleapis.com/token",
                "redirect_uris": [REDIRECT_URL]
            }
        },
        scopes=SCOPES
    )
    
    auth_url, _ = flow.authorization_url(prompt='consent')
    return f'<a href="{auth_url}">Login with Google</a>'

@app.route('/callback')
def callback():
    flow = Flow.from_client_config(
        {
            "web": {
                "client_id": CLIENT_ID,
                "client_secret": CLIENT_SECRET,
                "auth_uri": "https://accounts.google.com/o/oauth2/auth",
                "token_uri": "https://oauth2.googleapis.com/token",
                "redirect_uris": [REDIRECT_URL]
            }
        },
        scopes=SCOPES
    )
    
    flow.fetch_token(authorization_response=flask.request.url)
    credentials = flow.credentials
    
    # Save tokens
    with open('token.json', 'w') as f:
        f.write(credentials.to_json())
    
    return 'Authorization complete! You can close this window.'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8889)
```

---

## Part 5: Token Management and Refresh

Access tokens expire after 1 hour. Your app should automatically refresh them:

```javascript
// Set up token refresh callback
oauth2Client.on('tokens', (tokens) => {
  if (tokens.refresh_token) {
    // Save new refresh token
    const existingTokens = JSON.parse(fs.readFileSync(TOKEN_FILE));
    existingTokens.refresh_token = tokens.refresh_token;
    fs.writeFileSync(TOKEN_FILE, JSON.stringify(existingTokens, null, 2));
    console.log('Refresh token updated');
  }
});

// Load and set credentials
oauth2Client.setCredentials(JSON.parse(fs.readFileSync(TOKEN_FILE)));

// Auto-refresh happens automatically when API calls are made
```

---

## 🛠️ Real Problems We Faced and How to Fix

### Problem 1: "This app isn't verified" — OAuth Blocked by Google

**Symptom:**
```
Error 401: invalid_client
The OAuth client was not verified
```
OR users see:
```
"This app isn't verified" warning page
```

**Cause:** Google requires app verification for apps with sensitive scopes. Without verification, only test users can access.

**Fix:**

**For Internal Use (Fastest Solution):**
1. Go to OAuth consent screen
2. Add all company emails as test users
3. Test users can bypass verification

**For Public Use:**
1. Submit for Google verification:
   - Go to "OAuth consent screen" → "Publish App"
   - Complete the verification questionnaire
   - Wait 1-4 weeks for review

**Workaround for Development:**
```javascript
// Use Google OAuth Playground instead
// https://developers.google.com/oauthplayground/
// 1. Click gear icon
// 2. Check "Use your own OAuth credentials"
// 3. Enter your Client ID and Secret
// 4. Authorize and get tokens manually
```

---

### Problem 2: redirect_uri_mismatch Error

**Symptom:**
```
Error 400: redirect_uri_mismatch
{
  "error_description": ".redirect_uri_mismatch",
  "error_uri": "https://developers.google.com/admin-sdk/ calendarsResource/ errors# redirect_uri_mismatch"
}
```

**Cause:** The redirect URI in your code doesn't match what's registered in Google Cloud Console.

**Fix:**

1. Check exact redirect URI in your code:
```javascript
const REDIRECT_URL = 'http://localhost:8889/callback';
```

2. Go to Google Cloud Console → Credentials → Your OAuth Client

3. Under "Authorized redirect URIs", add the exact URI:
```
http://localhost:8889/callback
```
(Not just `http://localhost:8889` — must include the `/callback` path)

4. Save and try again

**Common Mistakes:**
- HTTP vs HTTPS (use HTTP for localhost)
- Missing or extra trailing slash
- Different port numbers
- Missing path (`/callback` vs `/oauth/callback`)

---

### Problem 3: "Private IP address not allowed" for redirect_uri

**Symptom:**
```
Error 400: invalid_request
Device id and device name are required for private IP
```

**Cause:** Google blocks OAuth redirects to private/local IP addresses (192.168.x.x, 10.x.x.x, localhost).

**Fix:**

**Option A — Use a Public Domain (Recommended):**
Set up a public domain (e.g., `your-server.example.com`) and configure DNS to point to your server. Add to Google Cloud Console:
```
http://your-server.example.com:8899/callback
```

**Option B — Use ngrok for Testing:**
```bash
ngrok http 8899
# You'll get a public URL like https://abc123.ngrok.io
```

Add to Google Cloud Console:
```
https://abc123.ngrok.io/callback
```


3. Update your code:
```javascript
const REDIRECT_URL = 'https://abc123.ngrok.io/callback';
```

**Important:** Your OAuth callback server must serve a proper HTML page (not just a blank response) at the root URL, otherwise browsers may show 404. See Part 4 for a complete server implementation.

**Option B — Use Google OAuth Playground:**
1. Go to https://developers.google.com/oauthplayground/
2. Click settings ⚙️
3. Check "Use your own OAuth credentials"
4. Enter Client ID and Client Secret
5. Authorize and exchange tokens through the playground

**Option C — Reverse SSH Tunnel:**
```bash
# From your server with public IP
ssh -R 8889:localhost:8889 user@your-public-server.com
```

---

### Problem 4: Refresh Token Not Being Returned

**Symptom:**
```javascript
console.log(tokens.refresh_token);
// undefined
```

**Cause:** `prompt: 'consent'` is needed for first-time authorization, otherwise Google might not return a refresh token if you've previously authorized.

**Fix:**

1. Revoke existing access:
   - Go to https://myaccount.google.com/permissions
   - Remove access for your app
   - Try again

2. Force consent prompt:
```javascript
const authUrl = oauth2Client.generateAuthUrl({
  access_type: 'offline',
  scope: SCOPES,
  prompt: 'consent'  // Force consent screen
});
```

3. Use incognito/private browser window

---

### Problem 5: Insufficient Permission for Some Operations

**Symptom:**
```javascript
// Error: Insufficient permission for that operation
```

**Cause:** The OAuth scope doesn't include the operation you're trying to perform.

**Fix:**

Check if you have the right scope:

| Operation | Required Scope |
|:---|:---|
| Read email | `gmail.readonly` |
| Send email | `gmail.send` |
| Modify email (archive, delete) | `gmail.modify` |
| Read calendar | `calendar` |
| Create calendar events | `calendar.events` |
| Upload to Drive | `drive.file` |

Add missing scopes in Google Cloud Console and re-authorize.

---

### Problem 6: "Access blocked: This app's request is invalid"

**Symptom:**
```
Error 400: access_denied
You can't sign in to this app because it doesn't comply with Google's OAuth 2.0 policy
```

**Cause:** Your app is trying to use sensitive scopes without proper verification, OR you're testing from a restricted environment.

**Fix:**

1. **Add test users** in OAuth consent screen (fastest for internal use)
2. **Publish the app** and complete Google verification
3. **Check OAuth scopes** — remove any unnecessary sensitive scopes
4. **Verify app domain** — if using a custom domain, ensure it's verified in Google Search Console

---

### Problem 7: CORS Errors When Using OAuth in Browser

**Symptom:**
```
Access-Control-Allow-Origin header missing
```

**Cause:** OAuth flows should not be initiated from browser JavaScript due to security risks.

**Fix:**

**Do:** Use server-side OAuth flow (Node.js/Python server as shown above)

**Don't:** Try to do OAuth directly from frontend JavaScript

The redirect-based OAuth flow requires a server-side component to securely handle tokens.

---

### Problem 8: Token Expired After a Few Hours — "invalid_client"

**Symptom:**
```
[FATAL] invalid_client
# OR after a while:
"Request had invalid authentication credentials"
```

**Cause:** Access tokens expire after 1 hour. If your refresh token is expired or was never issued, you cannot get a new access token.

**Fix:**

**Option A — Re-run OAuth Flow (Quick Fix):**
1. Delete the old token file:
```bash
rm /root/automation/credentials/google-tokens.json
```
2. Visit your OAuth authorization URL again:
```bash
node oauth-server.js
# Open http://your-server.example.com:8899/
```
3. Click "Login with Google" and authorize again
4. New tokens (including fresh refresh token) will be saved


**Prevention — Always request a refresh token:**
```javascript
const authUrl = oauth2Client.generateAuthUrl({
  access_type: 'offline',   // <-- This is critical!
  scope: SCOPES,
  prompt: 'consent'          // <-- Force to always show consent screen
});
```
The `access_type: 'offline'` parameter is essential — it tells Google to issue a refresh token. Without it, you only get a short-lived access token with no way to refresh it.

**Note:** Google may not issue a new refresh token if you've already authorized the app before. If `refresh_token` is `undefined` in your token file, revoke access at https://myaccount.google.com/permissions and re-run the flow.

---

### Problem 9: OAuth Server Shows "Not found" or 404 at Root URL

**Symptom:**
```
curl http://your-server.example.com:8899/
Not found
```

**Cause:** The callback server only handles `/callback` but returns 404 for the root URL. Browsers may show a confusing 404 page, making it unclear how to start the OAuth flow.


**Fix:**

Serve a proper HTML landing page at the root URL:

```javascript
const HTML_PAGE = `
<!DOCTYPE html>
<html>
<head><title>Google OAuth</title></head>
<body style="font-family:sans-serif;text-align:center;padding:60px">
  <h1>🔐 Google OAuth</h1>
  <p>Click below to authorize access to Google services</p>
  <a href="${authUrl}" style="background:#1a73e8;color:white;padding:14px 32px;border-radius:8px;text-decoration:none;font-size:16px">
    Login with Google
  </a>
</body>
</html>`;


const server = http.createServer((req, res) => {
  const parsed = url.parse(req.url, true);

  if (parsed.pathname === '/callback') {
    // Handle OAuth callback
    const code = parsed.query.code;
    if (code) {
      res.writeHead(200, { 'Content-Type': 'text/html' });
      res.end('<html><body><h1>✅ Done!</h1><p>You can close this window.</p></body></html>');
      // Exchange code for tokens...
      oauth2Client.getToken(code).then(({ tokens }) => {
        fs.writeFileSync(TOKEN_FILE, JSON.stringify(tokens));
        console.log('[SUCCESS] Tokens saved!');
        process.exit(0);
      });
    }
  } else {
    // Serve landing page at root
    res.writeHead(200, { 'Content-Type': 'text/html' });
    res.end(HTML_PAGE);
  }
});

server.listen(PORT, '0.0.0.0', () => {
  console.log('[READY] http://0.0.0.0:' + PORT + '/');
});
```

With this fix, users just open the server URL and see a clear "Login with Google" button — no need to know the auth URL in advance.

---


### Problem 10: "invalid_client" Even with Correct Credentials

**Symptom:**
```
[FATAL] invalid_client
```

**Cause:** The Client Secret in your code doesn't match the one in Google Cloud Console. Common causes:
- Typo in Client Secret string
- Extra or missing characters (e.g., `GOCSPX…Vla_` vs `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`)
- Client Secret changed after app verification

**Fix:**
1. Go to Google Cloud Console → Credentials → Your OAuth Client
2. Copy the Client Secret **exactly** (click the copy icon, don't type manually)
3. Update your code:
```javascript
const CLIENT_SECRET = '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';  // paste exact value
```
4. No extra spaces, no trailing quotes mistakes

**Tip:** Use environment variables instead of hardcoding credentials:
```bash
export GOOGLE_CLIENT_ID='your-client-id'
export GOOGLE_CLIENT_SECRET='your-client-secret'
```
```javascript
const CLIENT_ID = process.env.GOOGLE_CLIENT_ID;
const CLIENT_SECRET = process.env.GOOGLE_CLIENT_SECRET;
```

---


## 📊 Complete OAuth Flow Diagram

```
┌──────────────────────────────────────────────────────────────┐
│                        User Browser                            │
│                                                               │
│   1. User clicks "Login with Google"                         │
│   2. Redirects to Google Authorization Server                 │
│   3. User grants permissions                                 │
│   4. Google redirects back with ?code=XXXXX                   │
│   5. Page displays "Authorization Complete"                   │
└──────────────────────────────────────────────────────────────┘
                         ↓
┌──────────────────────────────────────────────────────────────┐
│                    Your Application Server                     │
│                                                               │
│   6. Exchange code for tokens                                 │
│   7. Save access_token + refresh_token                       │
│   8. Use access_token for API calls                          │
│   9. When expired, use refresh_token to get new one         │
└──────────────────────────────────────────────────────────────┘
                         ↓
┌──────────────────────────────────────────────────────────────┐
│                       Google API                                │
│                                                               │
│   Gmail API / Calendar API / Drive API / etc.                  │
└──────────────────────────────────────────────────────────────┘
```

---

## 🔒 Security Best Practices

1. **Never expose Client Secret in frontend code**
   - Always keep it server-side

2. **Store tokens securely**
   - Use encrypted storage
   - Never commit to version control

3. **Use minimal scopes**
   - Only request what you need
   - Reduces security risk and verification requirements

4. **Implement token refresh**
   - Access tokens expire
   - Refresh tokens for long-lived access

5. **Handle token revocation**
   - Users can revoke access anytime
   - Handle `invalid_grant` errors gracefully

---

## 📝 Summary Checklist

- [ ] Create Google Cloud project
- [ ] Enable required APIs
- [ ] Configure OAuth consent screen
- [ ] Add test users for internal use
- [ ] Create OAuth 2.0 credentials
- [ ] Add redirect URI
- [ ] Implement OAuth flow in application
- [ ] Handle token refresh
- [ ] Test with real user accounts
- [ ] Deploy with proper security

---

*This guide was written based on real-world implementation experience. For the latest Google OAuth 2.0 documentation, visit https://developers.google.com/identity/protocols/oauth2*

*Remove all placeholder values (client ID, secret, tokens) before publishing. Use environment variables for all sensitive credentials.*