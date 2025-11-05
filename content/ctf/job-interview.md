---
title: "Job Interview Writeup"
date: 2025-11-04T22:19:57-05:00
draft: false
---
# Job Interview Writeup

This is my writeup for `Job Interview` challenge in Forensics category at [root-me.org](https://www.root-me.org/en/Challenges/Forensic/Job-interview)

## Challenge Summary

Difficulty: 35 points

Description: You are invited to an interview for a forensics investigator position at the NSA. For your first technical evaluation they ask you to analyze this file. Prove to them that you’re a fitting candidate for this job.

Author: makhno

We are given a file named `image_forensics.e01` which is a image file format. As usualy, I would do some sanity checks for a disk file like using `file` command or some commands in `The Sleuth Kit` (TSK).

In this case, when do the command `fls` to look for file system, we are left with a result `Unsupported image file type (Tar Archive)`. Why is a disk image archived? 

## Solving the challenge

### Sanity checks
First, we need to edit the file format of this file to the correct format which is `.tar`. 

What happened after I changed this to a `.tar` archive and extract it? 

Surprise suprise, the output showed that this is not a tar archive 

![](/ctf/job-interview/image1.png)

How could this be? maybe this is not the correct format as well. 

We need to retrieve the raw format of this file. 

One way to do it is to use the commmand-line utility `ewfexport` from the libewf package.

![](/ctf/job-interview/image2.png)

During the exporting process, some information is required, such as filename without extension. The file was named as `img2` as the image above. 

Now that we have the raw file `img2.raw`, when do the `file` command again, we get this information: 

![](/ctf/job-interview/image3.png)

This means we are on the right track. After changing the file name to `img2.tar`, we can extract it using the command `tar -xvf img2.tar`. Open the extracted folder, we can see there is only one file named `bcache24.bmc`. 

The `.bmc` extension indicates this is a bitmap cache. 

### What is bitmap cache? 
According to [Microsoft](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpegdi/2bf92588-42bd-4527-8b3e-b90c56e292d2): 

> Bitmap caches are used by the client and server to store graphic bitmaps. Each bitmap cache holds bitmaps of a specified size in pixels (known as the "tile size"). If a bitmap does not fit into a single cache entry, the server uses a tiling algorithm to divide the bitmap into tiles that will fit into the cache entries so that they can be stored separately into the cache.

This was a solution to the sluggish RDP session when dial-up internet was used. 

The number appended to the end, usually a 2, 22, or 24 represent the quality of the images. The higher the number the higher the quality of the bitmap images generated. 

#### Remote Desktop Protocol (RDP) 
is a protocol in Windows machine that allows users to connect to another computer remotely with a graphical user interface (GUI). RDP bitmap cache was born to improve the RDP user experience. It stores bitmap sized images of the RDP sessions into a file so that the sessions can reuse these images. 

### How to view bmc files?
This [article](https://medium.com/@ronald.craft/blind-forensics-with-the-rdp-bitmap-cache-16e0c202f91c) introduces some tools to work with bitmap cache, including [bmc-tools](https://github.com/ANSSI-FR/bmc-tools) by ANSSI-FR, the French National Agency for the Security of Information Systems. 

```code
python3 bmc-tools.py -s ../bcache24.bmc -d ../extracted-bmc -b
[+++] Processing a single file: '../bcache24.bmc'.
[+++] Processing a file: '../bcache24.bmc'.
[===] 575 tiles successfully extracted in the end.
[===] Successfully exported 575 files.
```

We got 575 image files out of the bitmap cache. These files have the `.bmp` extension, a native Windows image format, so we can open this easily in Paint. Other OSes might need access to third party to view them. 

![](/ctf/job-interview/image4.png)
![](/ctf/job-interview/image5.png)

Articles that might be helpful:
https://www.cyberark.com/resources/threat-research-blog/explain-like-i-m-5-remote-desktop-protocol-rdp

https://www.allthingsdfir.com/do-you-even-bitmap-cache-bro/ 