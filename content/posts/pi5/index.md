---
title: "Pi5"
date: 2026-08-09T00:36:31-04:00
draft: false
---

## Flashing the OS on a USB stick

Starting from Pi5, you have more options to flash the OS other than using an SD card. 


## Network Connection 
In my case, it's easiest to connect the Pi with WiFi. 

I used NetworkManager `nmcli` for this task. 

`sudo nmcli dev wifi list` to check the available WiFi and `sudo nmcli dev wifi connect <SSID> --ask` to connect. The `--ask` flag is optional as it prompts for password.

Go to your DHCP server and reserve the IP address so that it won't hand the address to anything else. 

## SSH 

You can use Raspberry Pi Imager to bake your SSH public key into the image. In my case, Pi is already running, I will use `ssh-copy-id` to securely copy the pubkey from my computer to the Pi 5. 

Ensure that SSH is running on Pi. More on this [https://www.raspberrypi.com/documentation/computers/remote-access.html#:~:text=Enable%20the%20SSH%20server]()



