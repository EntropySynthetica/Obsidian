---
tags: [Linux]
title: chmod_on_only_file_or_dir
created: '2020-01-30T20:16:15.451Z'
modified: '2020-02-02T17:55:38.597Z'
---

# chmod on only file or dir
Created Wednesday 15 January 2020

Set 775 for folder and 664 for files:
`chmod -R ug=rw,o=r,a+X /path/to/folder/`

Only set on directory
`find /path/to/base/dir -type d -exec chmod 755 {} +`

Only set on file
`find /path/to/base/dir -type f -exec chmod 644 {} +`

