---
tags: [Linux]
title: APT_Cheat_Sheet
created: '2020-01-30T20:16:15.443Z'
modified: '2020-08-17T17:32:02.291Z'
---

# APT Cheat Sheet
Created Wednesday 24 May 2017

```
curl cheat.sh/apt
```

To search a package:
```
apt search package
```

To show package information:
```
apt show package
```

To fetch package list:
```
apt update
```

To download and install updates without installing new package:
```
apt upgrade
```

To download and install the updates AND install new necessary packages:
```
apt dist-upgrade
```

Full command:
```
apt update && apt dist-upgrade
```

To install a new package(s):
```
apt install <package name>
```

To uninstall package(s)
```
apt remove <package name>
```


To upgrade packages marked for not upgrade after Ubuntu version change
```
sudo apt install --only-upgrade <package name>
```