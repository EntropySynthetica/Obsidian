---
tags: [Linux]
title: Gluster HA File Server Install
created: '2020-07-22T21:31:44.231Z'
modified: '2020-07-30T13:16:49.504Z'
---

# Gluster HA File Server Install

## Server Setup

Requirements,

The server will be Ubuntu 18.04.  Data will be stored on a second HD on the server.  In this case /dev/sdb

### Run on all nodes

Install PPA and Install Gluster Server
`sudo add-apt-repository ppa:gluster/glusterfs-8`
`sudo apt update`
`apt install glusterfs-server`

Setup `/etc/hosts` with the dns names of all membeers of the cluster
```
192.168.0.11    labgluster01.lab
192.168.0.12    labgluster02.lab
192.168.0.13    labgluster03.lab
```

Setup Firewall to allow relevant ports
```
# Open Gluster Ports
iptables -A INPUT -p tcp --dport 24007:24008 -m state --state NEW -j ACCEPT
iptables -A INPUT -p tcp --dport 49152:49156 -m state --state NEW -j ACCEPT
```

Setup Data Partition

`sudo fdisk /dev/sdb`
press n for new partition
press p for primary volume
Accept the defaults for sizes
press w to write, there may be an error about re-reading failing.  It can be ignored. 

Format the drive
`mkfs.xfs -i size=512 /dev/sdb1`

Create the data directory
`mkdir -p /data/brick`

Add the drive to /etc/fstab
`echo '/dev/sdb1 /data/brick xfs defaults 1 2' >> /etc/fstab`

Mount the drive
`mount -a && mount`

Create the directory for our gluster volume
`mkdir -p /data/brick/gv0`

### From the first server run the following

Join server 2 into the cluster. 
`gluster peer probe labgluster02.lab`

Join Server 3 into the cluster.
`gluster peer probe labgluster03.lab`

Verify the cluster can see it's neighbors. 
`gluster peer status`

Create the Volume
`gluster volume create gv0 replica 3 labgluster01.lab:/data/brick/gv0 labgluster02.lab:/data/brick/gv0 labgluster03.lab:/data/brick/gv0`

Start the Volume
`gluster volume start gv0`

Verify the volume exists
`gluster volume info`

The results should look like the following
```
Volume Name: gv0
Type: Replicate
Volume ID: f25cc3d8-631f-41bd-96e1-3e22a4c6f71f
Status: Started
Snapshot Count: 0
Number of Bricks: 1 x 3 = 3
Transport-type: tcp
Bricks:
Brick1: labgluster01.lab:/data/brick/gv0
Brick2: labgluster02.lab:/data/brick/gv0
Brick3: labgluster03.lab:/data/brick/gv0
Options Reconfigured:
transport.address-family: inet
```

## Client Setup

`sudo add-apt-repository ppa:gluster/glusterfs-8`

`sudo apt update`

`sudo apt install glusterfs-client`

### Mount on Client

`sudo mount -t glusterfs labgluster01.lab:/gv0 /data`

Add the mount to /etc/fstab

`labgluster01.lab:/gv0 /data glusterfs defaults,_netdev 0 0`

## General commands for troubleshooting

### Show mounted clients
`gluster volume status <vol_name> clients`

### Get Volume Settings
`gluster volume info`

### Set the ping timeout for a failed of shutdown server. 
`gluster volume set <vol_name> network.ping-timeout <seconds>` # Default 42 Seconds

### Shell script to do a safe shutdown of the server
https://github.com/gluster/glusterfs/blob/master/extras/stop-all-gluster-processes.sh
