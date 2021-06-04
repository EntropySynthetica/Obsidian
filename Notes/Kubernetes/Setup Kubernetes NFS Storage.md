---
tags: [Kubernetes]
title: Setup Kubernetes NFS Storage
created: '2020-06-03T17:59:56.289Z'
modified: '2020-06-03T18:13:29.320Z'
---

# Setup Kubernetes NFS Storage

## Install NFS storage server
```
sudo apt install nfs-kernel-server
```

## Firewall rules
Enable TCP and UDP ports 2049

## Setup data partion for data to live in. 

Setup second drive with desired size of /data partition.

Set the new partition

`sudo fdisk /dev/sdb`
press n for new partition
press p for primary volume
Accept the defaults for sizes
press w to write, there may be an error about re-reading failing.  It can be ignored. 
Restart the system

```
sudo mkfs -t ext4 /dev/sdb1
sudo mkdir /data
sudo mount -t ext4 /dev/sdb1 /data/
```

Verify it mounts with:
`df -h`

add the following line to /etc/fstab
`/dev/sdb1       /data           ext4    errors=remount-ro       0       1`

restart the system and verify /data mounts with
`df -h`

## Create the nfs directory in /data
```
sudo mkdir -p /data/nfs
sudo chown nobody:nogroup /data/nfs
sudo chmod g+s /data/nfs
```

## Edit /etc/exports
```
"/data/nfs" <IP_ADDRESS1>(rw,sync,no_subtree_check) <IP_ADDRESS2>(rw,sync,no_subtree_check) <IP_ADDRESS3>(rw,sync,no_subtree_check)
```

## Restart NFS
```
sudo service nfs-kernel-server restart
```
