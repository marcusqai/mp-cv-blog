---
title: "SSH Key Pair Authentication: A Secure and Elegant Way to Login"
date: 2026-05-07T00:28:00+08:00
draft: false
tags:
  - tech
  - ssh
  - security
summary: "How to set up SSH key pair authentication for passwordless Linux server access."
---

To log into a Linux server without a password, the safest and most elegant approach is **SSH Key Pair Authentication**. Think of it as an electronic lock for your server — and you hold the only key.

Here's the step-by-step setup for **Client B** (your machine) and **Server A** (the target server).

---

## Phase 1: Generate Key Pair on Client B

First, create a key pair on the machine you'll connect from (Client B): a **Public Key** and a **Private Key**.

1. Open a terminal and run:
   ```bash
   ssh-keygen -t rsa -b 4096
   ```

2. When prompted for the storage location, press **Enter** to accept the default path (`~/.ssh/id_rsa`).

3. Set a passphrase for the private key:
   - For maximum convenience, press **Enter** twice to skip.
   - For enhanced security, set a passphrase that only you know on your local machine.

Once complete, you'll have two files:
- `id_rsa` — **Private Key**. Never share this with anyone. Keep it safe.
- `id_rsa.pub` — **Public Key**. This is the "lock" we'll place on Server A.

---

## Phase 2: Transfer the Public Key to Server A

Now install the "lock" (public key) onto Server A.

### Method 1: Automatic Transfer (Recommended)

On Client B, run:
```bash
ssh-copy-id username@server_a_ip
```

You'll be asked for Server A's password one final time. After that, the public key is automatically placed in the correct location.

### Method 2: Manual Setup

If you can't use the command above:

1. On Client B, display your public key:
   ```bash
   cat ~/.ssh/id_rsa.pub
   ```
   Copy the entire output.

2. Log into Server A, create the `.ssh` directory in the user's home:
   ```bash
   mkdir -p ~/.ssh
   chmod 700 ~/.ssh
   ```

3. Paste the copied content into `authorized_keys`:
   ```bash
   nano ~/.ssh/authorized_keys
   # Paste the content, then save and exit
   chmod 600 ~/.ssh/authorized_keys
   ```

---

## Phase 3: Fine-Tune Security (Optional)

To ensure everything works flawlessly, verify the SSH configuration on Server A.

1. Edit the config file:
   ```bash
   sudo nano /etc/ssh/sshd_config
   ```

2. Confirm these settings:
   - `PubkeyAuthentication yes` — Ensure key-based auth is enabled
   - `PasswordAuthentication no` — *Optional:* Set to `no` if you want to completely disable password login. **Only do this after confirming your key works — otherwise you'll lock yourself out.**

3. Restart SSH:
   ```bash
   sudo systemctl restart ssh
   ```

---

## Phase 4: Log In Elegantly

Now, from Client B, simply type:
```bash
ssh username@server_a_ip
```

The server silently verifies your identity and opens the door — no password needed.

---

> **Note on Permissions:**
> File permissions are at the heart of how SSH works. If the `.ssh` directory or its files have overly permissive settings (e.g., `777`), SSH will refuse to use the key for security reasons. Always follow the `700` (for `.ssh`) and `600` (for `authorized_keys`) permissions mentioned above.
