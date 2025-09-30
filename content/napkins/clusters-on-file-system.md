---
title: Clusters on File Systems
date: 2025-09-29
draft: false
---
There are many types of file systems (fs). What do we need to know to learn about disk analysis? 


FAT fs has a field that identifies how many sectors are in a cluster, and it must have a value that is a power of 2. 

A Linux tool that is used for partition recovery is `gpart`. 

The NTFS has 2048-byte clusters and 512-byte sectors.  

All file systems have slack space because all file systems allocate data in multiple-byte chunks instead of in individual bytes. Slack space is considered allocated data, but if you extract the unallocated data from a file system, it should not include the slack space of a file. 


There are two major ways to recover deleted files: metadata-based and application-based. 
Metadata-based recovery works when metadata from the deleted file still exists. In the case it was wiped or reallocated to a new file, then we need to rely on application-based method. 

The OS has a big decision here. 

Most of the time, we can use the metadata to view file contents, search for values, and locate deleted files. 


How does `fls` work? Why in some case, `file` can do a better job than `fls`? Let's dive a little bit in some of the most used The Sleuth Kit (TSK) commands. 

To effectively use `fls`, we should incorporate `mmls` at the same time in order to locate the block position of where the MBR is. 

In what way would you need to recover lost things? 

Imagine that you have a 5TB hard disk (HDD) that holds years of your family photos and videos. And one day, when you take it out to connect to a big monitor to see these old photos again, you drop the hard disk on the ground. 

Btw, this actually happened to my dad's HDD that held many photos of his children when they were young. 

