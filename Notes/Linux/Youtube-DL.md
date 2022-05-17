---
tags: [Linux]
title: Youtube-DL
created: '2020-01-30T20:16:16.043Z'
modified: '2020-02-02T17:47:33.874Z'
---

# Youtube-DL

To install it right away for all UNIX users (Linux, OS X, etc.), type:
```
sudo curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o /usr/local/bin/yt-dlp
sudo chmod a+rx /usr/local/bin/yt-dlp
```

Update App
`sudo yt-dlp -U`

Youtube download to MP3 example

`yt-dlp --extract-audio --audio-format mp3 https://www.youtube.com/watch?v=EP1mczaq3zo`


As of 2022 yt-dlp has replaced youtube-dl

Ref: https://github.com/yt-dlp/yt-dlp/
