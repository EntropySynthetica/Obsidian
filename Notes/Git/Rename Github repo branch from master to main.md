---
tags: [Git]
title: Rename Github repo branch from master to main
created: '2021-02-10T19:47:52.414Z'
modified: '2021-02-10T19:49:22.279Z'
---

# Rename Github repo branch from master to main

Go into github and click branche
Click the edit pencil to the right of the master branch name.
Rename the branch.

If you have a local clone, you can update it by running:

```
git branch -m master main
git fetch origin
git branch -u origin/main main
```
