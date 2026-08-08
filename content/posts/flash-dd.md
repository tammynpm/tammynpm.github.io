---
title: "Flash a usb using dd"
date: 2026-08-07T23:45:33-04:00
draft: false
---

simplest way to flash an ISO to USB on Arch Linux without installing another third party application like Balena Etcher or Rufus is to use a command that is most likely already installed by default on your Unix distro: `dd`. 

There are only a few simple steps to follow: 

1. Find your USB device `lsblk`
2. Unmount it if it's mounted `sudo umount /dev/sdX*` 
3. Flash the ISO 


`sudo dd if=<path_to_iso> of=</dev/sdX> bs=4M status=progress && sync`

`bs` sets the block size in bytes. The default value is 512 bytes, the smaller the value, the slower the operation takes. 

the `status=progress` option is for monitoring the progress of the operation. 

`dd` is a linux utility that can be used to copy at block level. Arch Wiki has a [detailed documentation on this command specifically](https://wiki.archlinux.org/title/Dd).
