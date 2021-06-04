---
tags: [Linux]
title: Trim_end_of_MP3_file
created: '2020-01-30T20:16:15.975Z'
modified: '2020-01-30T20:54:22.715Z'
---

# Trim end of MP3 file
Created Friday 23 June 2017

ffmpeg -t 30 -i inputfile.mp3 -acodec copy outputfile.mp3

-t = time in seconds of the part of the mp3 we want to keep. 

