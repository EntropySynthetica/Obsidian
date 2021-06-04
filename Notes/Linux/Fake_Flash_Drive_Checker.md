---
tags: [Linux]
title: Fake_Flash_Drive_Checker
created: '2020-01-30T20:16:15.535Z'
modified: '2020-01-30T20:40:49.187Z'
---

# Fake Flash Drive Checker
Created Friday 20 October 2017

f3write [--start-at=NUM] [--end-at=NUM] <PATH>
f3read  [--start-at=NUM] [--end-at=NUM] <PATH>
f3write will ask for the devices claimed size and fill it with generated files with a size of 1gb each. f3read will read all those files and see of they are complete and not broken. As an example the commands I used to test my ~128gb thumb drive:

~> f3write /media/username/1EB8021AB801F0D7/
Free space: 117.94 GB
Creating file 1.h2w ... OK!                           
...
Creating file 118.h2w ... OK!                         
Free space: 0.00 Byte
Average writing speed: 11.67 MB/s
Now to test whether the files are correctly stored:

~> f3read /media/username/1EB8021AB801F0D7/
  SECTORS      ok/corrupted/changed/overwritten
Validating file 1.h2w ... 2097152/        0/      0/      0
...
Validating file 118.h2w ... 1979488/        0/      0/      0

  Data OK: 117.94 GB (247346272 sectors)
Data LOST: 0.00 Byte (0 sectors)
   Corrupted: 0.00 Byte (0 sectors)
Slightly changed: 0.00 Byte (0 sectors)
 Overwritten: 0.00 Byte (0 sectors)
Average reading speed: 32.38 MB/s

The f3probe method

f3probe is another way to test the drives, not as accurate but faster since it does not write on the whole drive. You can read more about it on the tools website. If you want to be 100% sure, better use the h2testw method. As the developer describes on the website:

f3probe is the fastest way to identify fake drives and their real sizes.
and

Finally, thanks to f3probe being free software, and once f3probe is battle proven, f3probe could be embedded on smartphones, cameras, MP3 players, and other devices to stop once and for all the proliferation of fake flash.
There is also a usage example on the website:

$ sudo ./f3probe --destructive --time-ops /dev/sdb
[sudo] password for michel: 
F3 probe 6.0
Copyright (C) 2010 Digirati Internet LTDA.
This is free software; see the source for copying conditions.

WARNING: Probing normally takes from a few seconds to 15 minutes, but
 it can take longer. Please be patient.

Bad news: The device `/dev/sdb' is a counterfeit of type limbo

You can "fix" this device using the following command:
f3fix --last-sec=16477878 /dev/sdb

Device geometry:
 *Usable* size: 7.86 GB (16477879 blocks)
Announced size: 15.33 GB (32155648 blocks)
Module: 16.00 GB (2^34 Bytes)
Approximate cache size: 0.00 Byte (0 blocks), need-reset=yes
   Physical block size: 512.00 Byte (2^9 Bytes)

Probe time: 1'13"
 Operation: total time / count = avg time
  Read: 472.1ms / 4198 = 112us
 Write: 55.48s / 2158 = 25.7ms
 Reset: 17.88s / 14 = 1.27s
Note that it also returns a command that enables you to use the drive with it's real size, using f3fix.

The f3fix tool

f3fix enables users to use the real capacity of fake drives without losing data.
Install in Ubuntu

The described tools are part of the f3 package, which is at least available on Ubuntu 15.10. According to the website, there are some more tools that are available. To get them take a look at the website. To install the package just type into a terminal:

sudo apt-get install f3

