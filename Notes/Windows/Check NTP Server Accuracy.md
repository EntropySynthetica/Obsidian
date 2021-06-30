---
tags: [Windows]
title: Check NTP Server Accuracy
created: '2020-01-30T20:16:16.515Z'
modified: '2020-02-02T17:56:25.039Z'
---

# Check NTP Server Accuracy
Created Monday 03 April 2017

`w32tm /stripchart /computer:time.windows.com`


## Example from when Microsoft was having Time server issues. 

```
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

U:\>w32tm /stripchart /computer:time.windows.com
Tracking time.windows.com [13.66.62.111:123].
The current time is 4/3/2017 9:56:11 AM.
09:56:11 d:+00.0623991s o:-00.6887759s  [                         * |                           ]
09:56:13 error: 0x800705B4
09:56:16 d:+00.0467987s o:-02.6877832s  [                    *      |                           ]
09:56:18 d:+00.0467986s o:+09.9312044s  [                           |                          *]
09:56:20 d:+00.0467984s o:+11.1862354s  [                           |                          @]
09:56:22 error: 0x800705B4
09:56:25 d:+00.4524017s o:+11.3869026s  [                           |                          @]
09:56:27 error: 0x800705B4
09:56:31 error: 0x800705B4
09:56:34 d:+00.3432013s o:-04.1614944s  [                *          |                           ]
09:56:36 d:+00.4212015s o:-00.1474275s  [                           *                           ]
09:56:38 d:+00.2496007s o:-00.7941106s  [                         * |                           ]
09:56:41 error: 0x800705B4
09:56:44 d:+00.0935993s o:+12.5408467s  [                           |                          @]
09:56:46 d:+00.0467985s o:-00.0673628s  [                           *                           ]
09:56:48 d:+00.0467986s o:+09.3192275s  [                           |                         * ]
^C
U:\>w32tm /stripchart /computer:time.google.com
Tracking time.google.com [216.239.35.4:123].
The current time is 4/3/2017 9:56:53 AM.
09:56:53 d:+00.0312002s o:+00.0528812s  [                           *                           ]
09:56:55 d:+00.0156001s o:+00.0465458s  [                           *                           ]
09:56:57 d:+00.0156001s o:+00.0467283s  [                           *                           ]
09:57:00 d:+00.0156001s o:+00.0463062s  [                           *                           ]
09:57:02 d:+00.0156001s o:+00.0457992s  [                           *                           ]
09:57:04 d:+00.0156001s o:+00.0467021s  [                           *                           ]
09:57:06 d:+00.0156001s o:+00.0463978s  [                           *                           ]
09:57:08 d:+00.0156001s o:+00.0461759s  [                           *                           ]
09:57:10 d:+00.0156001s o:+00.0459693s  [                           *                           ]
09:57:12 d:+00.0156001s o:+00.0459968s  [                           *                           ]
^C
U:\>w32tm /stripchart /computer:us.pool.ntp.org
Tracking us.pool.ntp.org [66.135.44.92:123].
The current time is 4/3/2017 9:57:32 AM.
09:57:32 d:+00.0467265s o:+00.0557878s  [                           *                           ]
09:57:34 d:+00.0467236s o:+00.0440288s  [                           *                           ]
09:57:36 d:+00.0466869s o:+00.0440502s  [                           *                           ]
09:57:38 d:+00.0466929s o:+00.0444638s  [                           *                           ]
09:57:41 d:+00.0467285s o:+00.0440896s  [                           *                           ]
09:57:43 d:+00.0467197s o:+00.0453036s  [                           *                           ]
09:57:45 d:+00.0466619s o:+00.0451956s  [                           *                           ]
09:57:47 d:+00.0467262s o:+00.0451299s  [                           *                           ]
09:57:49 d:+00.0467544s o:+00.0447976s  [                           *                           ]
^C
U:\>
```
