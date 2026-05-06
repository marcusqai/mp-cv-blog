---
title: "SSH Key-Based Authentication: A Secure and Elegant Way to Login"
date: 2026-05-07T00:28:00+08:00
draft: false
tags:
  - ssh
  - linux
  - security
  - server
summary: "How to set up SSH key pair authentication for passwordless, secure server access."
---

To log into a Linux server without using a password, the safest and most elegant method is to use **SSH Key Pair Authentication**. It's like giving the server an electronic lock — and you hold the only key.

Here's a step-by-step guide for both the **Client B** (your local machine) and **Server A** (the remote server).

---

## Step 1: Generate the Key Pair on Client B

First, you need to create a pair of keys on the machine you'll be connecting from (Client B): a **Public Key** and a **Private Key**.

1. Open a terminal and run:
   ```bash
   ssh-keygen -t rsa -b 4096
   ```

2. When prompted for the file location, just press **Enter** to accept the default path (`~/.ssh/id_rsa`).

3. Set a passphrase for your private key:
   - For maximum convenience, press **Enter** twice to skip.
   - For extra security, set a passphrase — it's stored only on your local machine.

Once complete, you'll have two files:
- `id_rsa` — Your **private key**. Never share this with anyone. Keep it safe.
- `id_rsa.pub` — Your **public key**. This is the "lock" we'll place on Server A.

---

## Step 2: Transfer the Public Key to Server A

Now we need to install the "lock" (public key) onto Server A.

### Method 1: Automatic Transfer (Recommended)

On Client B, run:
```bash
ssh-copy-id username@server_a_ip
```
You'll be asked for Server A's password one last time. After that, the public key will be automatically placed in the correct location.

### Method 2: Manual Setup

If you can't use the command above:

1. On Client B, display your public key:
   ```bash
   cat ~/.ssh/id_rsa.pub
   ```
   Copy the entire output.

2. Log into Server A and create the `.ssh` directory in your home folder:
   ```bash
   mkdir -p ~/.ssh
   chmod 700 ~/.ssh
   ```

3. Paste the copied content into the `authorized_keys` file:
   ```bash
   nano ~/.ssh/authorized_keys
   # Paste the content, then save and exit
   chmod 600 ~/.ssh/authorized_keys
   ```

---

## Step 3: Fine-Tune Security (Optional)

To ensure everything works as expected, check the SSH configuration on Server A.

1. Edit the SSH config file:
   ```bash
   sudo nano /etc/ssh/sshd_config
   ```

2. Verify the following settings:
   - `PubkeyAuthentication yes` — Ensures key-based authentication is enabled.
   - `PasswordAuthentication no` — *Optional:* Set to `no` if you want to completely disable password login. **Only do this after confirming your key works — otherwise you'll lock yourself out.**

3. Restart the SSH service:
   ```bash
   sudo systemctl restart ssh
   ```

---

## Step 4: Log In Elegantly

Now, from Client B, simply run:
```bash
ssh username@server_a_ip
```
The server will quietly verify your identity and grant you access — no password required.

---

### A Quick Note

**Permissions are at the heart of SSH.** If the `.ssh` directory or its files have overly permissive settings (e.g., `777`), SSH will refuse to use the key for security reasons. Always follow the `700` (for `.ssh`) and `600` (for `authorized_keys`) permissions mentioned above.
