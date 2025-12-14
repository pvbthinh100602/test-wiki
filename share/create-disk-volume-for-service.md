---
title: Create New Disk Volume for Service on BKIT Server
description: Step-by-step guide for creating and mounting a new logical volume for services on the BKIT self-hosted server using LVM
published: true
date: 2025-12-14T17:00:48.472Z
tags: 
editor: markdown
dateCreated: 2025-12-14T17:00:48.472Z
---

# Create New Disk Volume for Service on BKIT Server

## 🚀 Introduction

This guide walks you through the process of creating a new disk volume (logical volume) for a new service on the BKIT self-hosted server. The process involves creating a logical volume from an existing volume group, formatting it with a filesystem, creating a mount point, and configuring it to mount automatically on system boot.

## 📋 Prerequisites

Before starting, ensure you have:
- **Root or sudo access** to the BKIT server
- **An existing volume group** (e.g., `vg_data`) with available space
- **Knowledge of the service name** and required disk size
- **SSH access** to the server (see [SSH Access Guide](../general/ssh-access-to-server.md))

## 🔧 Step-by-Step Guide

### Step 1: Create Logical Volume

Create a new logical volume with the desired size and name. Replace the values according to your service:

```bash
sudo lvcreate -L 100G -n lv_openproject vg_data
```

**Parameters:**
- `-L 100G`: Size of the logical volume (adjust as needed: 50G, 200G, etc.)
- `-n lv_openproject`: Name of the logical volume (replace `openproject` with your service name)
- `vg_data`: Name of the volume group (verify this exists on your system)

**Example for different services:**
```bash
# For a database service
sudo lvcreate -L 200G -n lv_postgresql vg_data

# For a file storage service
sudo lvcreate -L 500G -n lv_file_storage vg_data
```

### Step 2: Verify Logical Volume Creation

List all logical volumes to confirm your new volume was created successfully:

```bash
sudo lvs
```

You should see your newly created logical volume in the list with the correct size and volume group.

### Step 3: Create Filesystem

Format the logical volume with the ext4 filesystem:

```bash
sudo mkfs.ext4 /dev/vg_data/lv_openproject
```

**Note:** Replace `lv_openproject` with your actual logical volume name. This command will format the volume and may take a few minutes depending on the size.

**Alternative filesystems:**
- For XFS: `sudo mkfs.xfs /dev/vg_data/lv_openproject`
- For ext3: `sudo mkfs.ext3 /dev/vg_data/lv_openproject`

### Step 4: Create Mount Point Directory

Create a directory where the volume will be mounted:

```bash
sudo mkdir -p /mnt/openproject
```

**Best practices for mount points:**
- Use `/mnt/` for temporary or service-specific mounts
- Use `/srv/` for service data (alternative location)
- Use descriptive names that match your service (e.g., `/mnt/postgresql`, `/mnt/nextcloud`)

### Step 5: Get Volume UUID

Retrieve the UUID (Universally Unique Identifier) of the logical volume. This is needed for the fstab entry:

```bash
sudo blkid /dev/vg_data/lv_openproject
```

**Output example:**
```
/dev/vg_data/lv_openproject: UUID="a1b2c3d4-e5f6-7890-abcd-ef1234567890" TYPE="ext4"
```

**Copy the UUID value** - you'll need it in the next step.

### Step 6: Configure Automatic Mounting

Edit the `/etc/fstab` file to add an entry for automatic mounting on boot:

```bash
sudo vi /etc/fstab
```

**Add the following line** (replace values with your actual UUID, mount point, and options):

```bash
UUID=a1b2c3d4-e5f6-7890-abcd-ef1234567890 /mnt/openproject ext4 defaults 0 2
```

**fstab entry format:**
```
UUID=<your-uuid> <mount-point> <filesystem-type> <options> <dump> <pass>
```

**Field explanations:**
- **UUID**: The UUID from Step 5
- **Mount point**: Directory created in Step 4 (e.g., `/mnt/openproject`)
- **Filesystem type**: `ext4` (or `xfs`, `ext3`, etc.)
- **Options**: `defaults` (common options: rw, suid, dev, exec, auto, nouser, async)
- **Dump**: `0` (backup flag, 0 = no backup)
- **Pass**: `2` (fsck order, 2 = check after root filesystem)

**Alternative using device path** (less recommended, UUID is preferred):
```bash
/dev/vg_data/lv_openproject /mnt/openproject ext4 defaults 0 2
```

**Save and exit:**
- In `vi`: Press `Esc`, type `:wq`, press `Enter`
- In `nano`: Press `Ctrl+X`, then `Y`, then `Enter`

### Step 7: Mount the Volume

Test the fstab configuration and mount the volume:

```bash
sudo mount -a
```

This command mounts all filesystems defined in `/etc/fstab`. If there are no errors, your volume is now mounted.

**Verify the mount:**
```bash
df -h | grep openproject
```

Or check all mounts:
```bash
mount | grep openproject
```

## ✅ Verification

After completing all steps, verify everything is working correctly:

1. **Check if volume is mounted:**
   ```bash
   df -h /mnt/openproject
   ```

2. **Test write permissions:**
   ```bash
   sudo touch /mnt/openproject/test.txt
   sudo rm /mnt/openproject/test.txt
   ```

## 📞 Summary

You've learned how to:
- ✅ Create a logical volume from an existing volume group
- ✅ Format the volume with a filesystem
- ✅ Create a mount point directory
- ✅ Retrieve the volume UUID
- ✅ Configure automatic mounting via fstab
- ✅ Mount and verify the volume

Following these steps will allow you to create and configure new disk volumes for services on the BKIT server, ensuring they are properly mounted and available for use.

