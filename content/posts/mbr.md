---
title: "Mbr"
date: 2025-09-25T19:02:31-04:00
draft: true
---

Master Boot Record or MBR, aka DOS style boot record, contains three things

1. Boot Code
2. Partition Table
3. Signature

MBRs on USBs don't contain bootcode. 
MBRs on harddrives do contain bootcode. The firmware handles the bootcode. The bootcode are is used for other information instead. 


The signature value is the last two bytes of a sector containing a MBR. The signature value is `55 AA`. These two bytes don't guarantee a valid MBR, but without them, the disk does not have MBR.


### Comparing to GUID 
MBR is the legacy format. GPT or GUID Partition Table is the modern day style of partitioning. 


## Extended partitions

First 3 parittions give entries in the MBR. Then the last partition of the MBR turns into a "Primary Extended Partition". 

Extended Boot Record is simply a version of MBR with 2 entries:
* First is the data partition that points to the data partition, containing the partition's length
* Second is the address fo the next EBR relative to the start of the primary extended patition

EBRs are bcoming more rare as GPTs are becoming standard. 


# GPT (tbd)


## CHS vs. LBA

