---
title: "Osu Forensics Writeup"
date: 2025-10-25T17:47:52-04:00
draft: false
---
# Osu Forensics Writeup

This is my writeup for the only forensics challenge `map dealer` in `osu ctf 2025` on Oct 24, 2025.

## Challenge Summary

Difficulty: 3/5

Description: `We have confiscated a USB drive from sahuang, whom we were informed was trying to sell a beatmap containing some confidential data of the community to the dark web. However, the beatmap was nowhere to be found from the drive when we mounted it to our computer. Can you help recover it?`
An archive `forensics_map-dealer.tar.gz` was given. 

![](static/ctf/image.png)

Since I know this is a disk forensics, I immediately think of using the tool `The Sleuth Kit` (TSK) which is often used in law enforcements. It is made by Brian Carrier, who also created Autopsy, the GUI version of TSK. Additionally, `file SanDisk.E01` tells us that this is a disk image. 

First, we want to see what kind of file system does it have. The command [`fls`](https://www.sleuthkit.org/sleuthkit/man/fls.html), according to the manual, lists file and directory naems in a disk image.
![](static/ctf/image-1.png)

The asterisk `*` indicates a deleted file. The next step is to get the inode number next to it. It is `8202` for the deleted file `sahuang - secret map.osz` in this case. 

[`icat`](https://www.sleuthkit.org/sleuthkit/man/icat.html) outputs the contents of a file given an inode number. We will use this to retrieves the deleted file. 

`icat SanDisk.E01 -o 8202 > recovered.osz`

To maintain the original format of the deleted file, we need to keep the `.osz` extension when carving the data into a new file. 

Using `file` command, we know that this is a zip archive data, so we change the extension of this file into `.zip`. 

In the `recovered.zip` archive, we can see the handwritten flag in the `flag.png` file. 

{{< figure
src="/static/ctf/flag.png"
caption="content of flag.png"
class="ma0 w-75">
}}

Answer: **osu{I_hope_my_h4ndrwr1ting_is_readable_xd}**


