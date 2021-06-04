---
tags: [Linux]
title: Linux_SSD_Trim
created: '2020-01-30T20:16:15.779Z'
modified: '2020-02-02T17:46:20.945Z'
---

# Linux SSD Trim
Created Monday 20 August 2018

`fstrim -v /`

```
Usage:
 fstrim [options] <mount point>

Discard unused blocks on a mounted filesystem.

Options:
 -a, --all           trim all mounted filesystems that are supported
 -o, --offset <num>  the offset in bytes to start discarding from
 -l, --length <num>  the number of bytes to discard
 -m, --minimum <num> the minimum extent length to discard
 -v, --verbose       print number of discarded bytes

 -h, --help     display this help and exit
 -V, --version  output version information and exit

For more details see fstrim(8).
```
