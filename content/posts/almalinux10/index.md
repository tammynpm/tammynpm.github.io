---
title: "Docker Installation on AlmaLinux10"
date: 2026-08-10T12:14:38-04:00
draft: false
---

Background info:

- [ ] AlmaLinux10 CloudInit Qcow2 

```
sudo dnf install -y kernel-modules-extra
Last metadata expiration check: 0:16:22 ago on Mon 10 Aug 2026 04:02:02 PM UTC.
Dependencies resolved.
=======================================================================================================
 Package                      Architecture      Version                           Repository      Size
=======================================================================================================
Installing:
 kernel-modules-extra         x86_64_v2         6.12.0-211.43.1.el10_2            baseos         3.1 M

Transaction Summary
=======================================================================================================
Install  1 Package

Total download size: 3.1 M
Installed size: 1.4 M
Downloading Packages:
kernel-modules-extra-6.12.0-211.43.1.el10_2.x86_64_v2.rpm              6.8 MB/s | 3.1 MB     00:00    
-------------------------------------------------------------------------------------------------------
Total                                                                  3.9 MB/s | 3.1 MB     00:00     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                               1/1 
  Installing       : kernel-modules-extra-6.12.0-211.43.1.el10_2.x86_64_v2                         1/1 
  Running scriptlet: kernel-modules-extra-6.12.0-211.43.1.el10_2.x86_64_v2                         1/1 

Installed:
  kernel-modules-extra-6.12.0-211.43.1.el10_2.x86_64_v2                                                

Complete!
```


Then reboot. 

`find /lib/modules/$(uname -r) -iname '*addrtype*'` should give the result 
`/lib/modules/6.12.0-211.43.1.el10_2.x86_64_v2/kernel/net/netfilter/xt_addrtype.ko.xz`

```
sudo modprobe xt_addrtype
lsmod | grep addrtype
sudo systemctl start docker
sudo systemctl status docker
```



