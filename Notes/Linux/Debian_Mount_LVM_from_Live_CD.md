---
tags: [Linux]
title: Debian_Mount_LVM_from_Live_CD
created: '2020-01-30T20:16:15.475Z'
modified: '2020-07-26T02:48:20.921Z'
---

# Debian Mount LVM from Live CD
Created Friday 09 December 2016


Step 1: Boot to a Debian live CD.  
Step 2: Install LVM to mound root


`apt-get install lvm2`

Step 3:
Run pvscan to scan all disks for physical volume:

`pvscan`

Step 4:
Run vgscan to scan disks for volume groups:

`vgscan`

Step 5: Activate volume groups:

`vgchange -a y`

Step 6: Run `lvscan` to scan all disks for logical volume.

Step 7: Run Disk check on Vol Group

`fsck /dev/VolGroup0/root`

Step 8 (Optional): Mount your root or another devices:


`mount /dev/VolGroup0/root /mnt`



