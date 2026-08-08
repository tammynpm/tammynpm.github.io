---
title: "Tailscale Fix"
date: 2025-12-25T20:18:51-05:00
draft: false
---
# Debugging Proxmox enterprise APT repositories

Issue: 

When we install Proxmox VE, the installer always enables the enterprise repo. This enterprise version requires a paid subscription token if we want to use the `apt` packager manager. Hence, we will use the community APT repo. 

First we need to edit two files: 
```bash
nano /etc/apt/sources.list.d/pve-enterprise.sources/
nano /etc/apt/sources.list.d/ceph.list/
```

We append the line `Enabled: no` at the end of those files. 

Then `sudo apt update` again. 