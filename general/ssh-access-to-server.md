---
title: How to Access BKIT Server via SSH
description: A step-by-step guide for setting up SSH access to servers using private key authentication, including SSH agent configuration and troubleshooting tips.
published: true
date: 2025-11-20T15:03:33.438Z
tags: 
editor: markdown
dateCreated: 2025-11-19T17:28:43.871Z
---

# SSH Access to BKIT Server

## 🔐 Introduction

This guide will walk you through the process of accessing a server using SSH. SSH (Secure Shell) is a secure protocol for remote access to servers. This guide covers two methods: password-based authentication (initial setup) and certificate-based authentication (recommended for enhanced security).

## 🚀 Method 1: Password-Based Access (Initial Setup)

### 1. 📞 Contact Admin for Account

Contact your system administrator to:
- Request a server account (username and password)
- Get the server hostname or IP address
- Confirm the SSH port (default is 22)

### 2. 🔌 Connect to Server Using Password

Now you're ready to connect to the server using SSH with your password:

1. **Basic SSH connection**:
   ```powershell
   ssh username@hostname
   ```
   Replace:
   - `username` with your server username
   - `hostname` with the server IP address or hostname

2. **Example access to BKIT server**:
   ```powershell
   ssh username@115.73.209.204
   ```

3. **Enter your password** when prompted.

4. **First-time connection**: You'll see a message asking to verify the server's fingerprint. Type `yes` to continue.

5. **You're in!** Once connected, you'll see the server's command prompt.

## ⭐ Method 2: Certificate-Based Access (Recommended)

Certificate-based authentication provides enhanced security and convenience. This method is optional but highly recommended.

### 1. 🔑 Generate SSH Key Pair

1. **Open PowerShell** (regular user, not admin):
   - Press `Windows Key + X`
   - Select "Windows PowerShell" or "Terminal"

2. **Generate a new SSH key pair**:
   ```powershell
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```
   Or if ed25519 is not supported, use:
   ```powershell
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   ```

3. **When prompted**:
   - Press Enter to accept the default file location (`C:\Users\YourUsername\.ssh\id_ed25519`)
   - Optionally enter a passphrase for extra security (recommended)
   - Confirm the passphrase if you entered one

4. **Your keys are created**:
   - Private key: `C:\Users\YourUsername\.ssh\id_ed25519` (keep this secret!)
   - Public key: `C:\Users\YourUsername\.ssh\id_ed25519.pub` (send this to admin)

### 2. 📤 Send Public Key to Admin

1. **Send the public key to your system administrator** via email or secure channel

2. **Wait for the admin to process your request** and send you back a certificate file

### 3. 📥 Receive Certificate from Admin

1. **Save the certificate file** provided by the admin to your `.ssh` folder:
   - Location: `C:\Users\YourUsername\.ssh\`
   - Name it something meaningful like `id_ed25519-cert.pub` or `user-cert.pub`

2. **Set proper permissions** (important for security):
   - Right-click on the certificate file → Properties → Security
   - Remove all users except your own account
   - Ensure only you have read access

### 4. 🔧 Enable SSH Agent (Optional but Recommended)

SSH Agent is a background program that can store your authentication credentials in memory, making connections more convenient.

#### What is SSH Agent?

SSH Agent securely manages your keys and certificates in memory, eliminating the need to enter passphrases repeatedly. It automatically provides credentials to SSH when needed.

**Benefits**:
- No need to repeatedly enter passphrases
- Keys and certificates are stored securely in memory
- Automatically provides credentials to SSH connections
- More secure than storing keys in plain text

#### Enable SSH Agent on Windows

1. **Open PowerShell as Administrator**:
   - Press `Windows Key + X`
   - Select "Windows PowerShell (Admin)" or "Terminal (Admin)"
   - Click "Yes" when prompted by User Account Control

2. **Start the SSH Agent service**:
   ```powershell
   Set-Service -StartupType Automatic
   Start-Service ssh-agent
   ```

3. **Verify the service is running**:
   ```powershell
   Get-Service ssh-agent
   ```
   You should see the status as "Running".


### 5. 🔑 Add Certificate to SSH Agent

1. **Add your private key to the SSH agent**:
   ```powershell
   ssh-add C:\Users\YourUsername\.ssh\id_ed25519
   ```
   Replace `id_ed25519` with your actual private key filename.

2. **If your key has a passphrase**, you'll be prompted to enter it once. After that, the agent will remember it.

3. **Verify the key was added**:
   ```powershell
   ssh-add -l
   ```
   You should see your key listed with its fingerprint.

### 6. 🔌 Connect to Server Using Certificate

Now you can connect using your certificate:

1. **Basic SSH connection** (SSH will automatically use the certificate):
   ```powershell
   ssh username@hostname
   ```

2. **Example access to BKIT server**:
   ```powershell
   ssh username@115.73.209.204
   ```

3. **If you need to specify the certificate explicitly if not use ssh-agent**:
   ```powershell
   ssh -i C:\Users\YourUsername\.ssh\id_ed25519 username@115.73.209.204
   ```

4. **You're in!** The certificate will be used automatically, and you won't need to enter a password.

## 🔍 Troubleshooting

### Common Issues and Solutions

**Issue**: "Permission denied (publickey)"
- **Solution**: Ensure your key is added to the SSH agent (`ssh-add -l` to verify)
- Check that the key file has correct permissions
- Verify you're using the correct username
- For certificate method: Ensure the certificate file is in the `.ssh` folder

**Issue**: "Could not open a connection to your authentication agent"
- **Solution**: Make sure SSH agent service is running (`Get-Service ssh-agent`)
- Start the service: `Start-Service ssh-agent`

**Issue**: "Host key verification failed"
- **Solution**: Remove the old host key from `C:\Users\YourUsername\.ssh\known_hosts`
- Or edit the file and remove the line with the problematic host

**Issue**: "Connection timed out"
- **Solution**: Verify the server IP/hostname is correct
- Check if you're connected to the correct network/VPN
- Confirm the SSH port is correct
- Contact admin to check firewall rules

**Issue**: "Certificate invalid" or "Certificate expired"
- **Solution**: Contact admin to issue a new certificate
- Check the certificate expiration date

## ✅ Best Practices

1. **Use certificate-based authentication**: More secure than password authentication
2. **Keep your private key secure**: Never share it or commit it to version control
3. **Use SSH agent**: Always use SSH agent to manage your keys securely
4. **Protect keys with passphrases**: Add an extra layer of security to your private keys
5. **Verify host fingerprints**: Always verify server fingerprints on first connection
6. **Close connections**: Properly exit SSH sessions (`exit` or `Ctrl+D`)
7. **Regular certificate renewal**: Contact admin before certificates expire

## 📞 Contact Admin for Support

If you encounter any issues or need additional resources, contact your system administrator for:
- **Connection problems**: Can't connect to the server
- **Permission issues**: Access denied errors
- **Certificate problems**: Certificate not working or expired
- **Key generation help**: Assistance with generating SSH keys
- **Additional access**: Need access to other servers or services
- **Firewall issues**: Connection blocked by firewall

## 📝 Summary

You've learned how to:
- ✅ Contact admin for SSH account (username and password)
- ✅ Connect to server using password authentication
- ✅ Generate SSH key pair (optional but recommended)
- ✅ Send public key to admin and receive certificate
- ✅ Enable and use SSH agent
- ✅ Connect to server using certificate authentication
- ✅ Know when to contact admin for help

Following these steps will allow you to securely access servers using SSH, with the recommended certificate-based method providing enhanced security and convenience.
