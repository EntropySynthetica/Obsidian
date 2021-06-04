---
tags: [Database, Linux, MySQL]
title: Percona_Cluster_Setup_Guide
created: '2020-01-30T20:16:15.851Z'
modified: '2020-05-06T16:33:45.734Z'
---

# Percona Cluster Setup Guide
Created Tuesday 27 March 2018

Assumptions, Using Ubuntu base image. 

In this example IPs are as following, replace with your IPs

PerconaDB 01 = 172.28.0.10
PerconaDB 02 = 172.28.0.20
PerconaDB 03 = 172.28.0.30

# Set Firewall allows.
TCP Ports 3306, 4444, 4567 and 4568 Need to be open.

edit [/home/sysadmin/firewall.sh](file:///home/sysadmin/firewall.sh)

Add these lines,

#Allow inbound ports for Percona SQL
```
iptables -A INPUT -p tcp --dport 3306 -m state --state NEW -j ACCEPT
iptables -A INPUT -p tcp --dport 4444 -m state --state NEW -j ACCEPT
iptables -A INPUT -p tcp --dport 4567 -m state --state NEW -j ACCEPT
iptables -A INPUT -p tcp --dport 4568 -m state --state NEW -j ACCEPT
```


Edit [/etc/hosts.allow](file:///etc/hosts.allow)

Make sure an allow is put in for the servers local subnet, and any subnets where legit connections may come from. 

example:
ALL: 172.26.
ALL: 172.20.
ALL: 192.168.148.
ALL: 172.28.



# Setup Data Partition

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



# Install Percona

## Install Percona on all DB Servers

```
sudo apt remove apparmor
wget https://repo.percona.com/apt/percona-release_latest.$(lsb_release -sc)_all.deb
sudo dpkg -i percona-release_latest.$(lsb_release -sc)_all.deb
sudo apt update
sudo apt install percona-xtradb-cluster-full-57
```

During install set the mysql root password.   
`sudo service mysql stop`

Set SQL to use the data drive:

```
cd /
cd var
cd lib
sudo cp -r -p mysql /data
```

edit /etc/mysql/percona-xtradb-cluster.conf.d/mysqld.cnf

Edit datadir=/data/mysql 


## Adjust the config on the first DB server:

edit /etc/mysql/percona-xtradb-cluster.conf.d/wsrep.cnf to look like the following. 

```
[mysqld]
# Path to Galera library
wsrep_provider=/usr/lib/galera3/libgalera_smm.so

# Cluster connection URL contains IPs of nodes
#If no IP is found, this implies that a new cluster needs to be created,
#in order to do that you need to bootstrap this node
wsrep_cluster_address=gcomm://172.28.0.10,172.28.0.20,172.28.0.30

# In order for Galera to work correctly binlog format should be ROW
binlog_format=ROW

# MyISAM storage engine has only experimental support
default_storage_engine=InnoDB

# Slave thread to use
wsrep_slave_threads= 8

wsrep_log_conflicts

# This changes how InnoDB autoincrement locks are managed and is a requirement for Galera
innodb_autoinc_lock_mode=2

# Node IP address
wsrep_node_address=172.28.0.10
# Cluster name
wsrep_cluster_name=pxc-cluster

#If wsrep_node_name is not specified,  then system hostname will be used
wsrep_node_name=pxc-cluster-node-1

#pxc_strict_mode allowed values: DISABLED,PERMISSIVE,ENFORCING,MASTER
pxc_strict_mode=ENFORCING

# SST method
wsrep_sst_method=xtrabackup-v2

#Authentication for SST method
wsrep_sst_auth=sstuser:<sst_password>
```


## Initialize the cluster with:

`sudo /etc/init.d/mysql bootstrap-pxc`
or
`sudo service mysql bootstrap-pxc`


## Check the cluster status:

`sudo mysql -u root -p`
`show status like 'wsrep%';`

Check the following fields, make sure they look like the following. 

```
 wsrep_local_state_comment        | Synced 
 wsrep_cluster_status             | Primary 
 wsrep_cluster_size               | 1  
 wsrep_local_state                | 4  
 wsrep_connected                  | ON 
 wsrep_ready                      | ON    
```


## Create the sstuser, this will be used for inter-process communication between the db servers.

```
CREATE USER 'sstuser'@'localhost' IDENTIFIED BY '<sst_password>';
GRANT RELOAD, LOCK TABLES, PROCESS, REPLICATION CLIENT ON *.* TO 'sstuser'@'localhost';
FLUSH PRIVILEGES;
```


# Adjust the config on the second db server:

edit /etc/mysql/percona-xtradb-cluster.conf.d/wsrep.cnf to look like the following. 

```
[mysqld]
# Path to Galera library
wsrep_provider=/usr/lib/galera3/libgalera_smm.so

# Cluster connection URL contains IPs of nodes
#If no IP is found, this implies that a new cluster needs to be created,
#in order to do that you need to bootstrap this node
wsrep_cluster_address=gcomm://172.28.0.10,172.28.0.20,172.28.0.30

# In order for Galera to work correctly binlog format should be ROW
binlog_format=ROW

# MyISAM storage engine has only experimental support
default_storage_engine=InnoDB

# Slave thread to use
wsrep_slave_threads= 8

wsrep_log_conflicts

# This changes how InnoDB autoincrement locks are managed and is a requirement for Galera
innodb_autoinc_lock_mode=2

# Node IP address
wsrep_node_address=172.28.0.20
# Cluster name
wsrep_cluster_name=pxc-cluster

#If wsrep_node_name is not specified,  then system hostname will be used
wsrep_node_name=pxc-cluster-node-2

#pxc_strict_mode allowed values: DISABLED,PERMISSIVE,ENFORCING,MASTER
pxc_strict_mode=ENFORCING

# SST method
wsrep_sst_method=xtrabackup-v2

#Authentication for SST method
wsrep_sst_auth=sstuser:<sst_password>
```


## Initialize the server:

`sudo /etc/init.d/mysql start`
or
`sudo service mysql start`


## Check the cluster status:

`sudo mysql -u root -p`
`show status like 'wsrep%';`

Check the following fields, make sure they look like the following. 

```
 wsrep_local_state_comment        | Synced 
 wsrep_cluster_status             | Primary 
 wsrep_cluster_size               | 2 
 wsrep_local_state                | 4  
 wsrep_connected                  | ON 
 wsrep_ready                      | ON    
```


# Adjust the config on the third db server:

edit /etc/mysql/percona-xtradb-cluster.conf.d/wsrep.cnf to look like the following. 

```
[mysqld]
# Path to Galera library
wsrep_provider=/usr/lib/galera3/libgalera_smm.so

# Cluster connection URL contains IPs of nodes
#If no IP is found, this implies that a new cluster needs to be created,
#in order to do that you need to bootstrap this node
wsrep_cluster_address=gcomm://172.28.0.10,172.28.0.20,172.28.0.30

# In order for Galera to work correctly binlog format should be ROW
binlog_format=ROW

# MyISAM storage engine has only experimental support
default_storage_engine=InnoDB

# Slave thread to use
wsrep_slave_threads= 8

wsrep_log_conflicts

# This changes how InnoDB autoincrement locks are managed and is a requirement for Galera
innodb_autoinc_lock_mode=2

# Node IP address
wsrep_node_address=172.28.0.30
# Cluster name
wsrep_cluster_name=pxc-cluster

#If wsrep_node_name is not specified,  then system hostname will be used
wsrep_node_name=pxc-cluster-node-3

#pxc_strict_mode allowed values: DISABLED,PERMISSIVE,ENFORCING,MASTER
pxc_strict_mode=ENFORCING

# SST method
wsrep_sst_method=xtrabackup-v2

#Authentication for SST method
wsrep_sst_auth=sstuser:<sst_password>
```


## Initialize the server:

`sudo /etc/init.d/mysql start`
or
`sudo service mysql start`



## Check the cluster status:

`sudo mysql -u root -p`
`show status like 'wsrep%';`

Check the following fields, make sure they look like the following. 

```
 wsrep_local_state_comment        | Synced 
 wsrep_cluster_status             | Primary 
 wsrep_cluster_size               | 3
 wsrep_local_state                | 4  
 wsrep_connected                  | ON 
 wsrep_ready                      | ON    
```




# ProxySQL install:

## On one of the db cluster servers create a user for proxysql:

`sudo mysql -u root -p`

```
CREATE USER 'proxysql'@'%' IDENTIFIED BY '<proxy_password>';
GRANT ALL ON *.* TO 'proxysql'@'%';
FLUSH PRIVILEGES;
```


## On the proxysql server:

```
sudo apt remove apparmor
wget https://repo.percona.com/apt/percona-release_latest.$(lsb_release -sc)_all.deb
sudo dpkg -i percona-release_latest.$(lsb_release -sc)_all.deb
sudo apt update
sudo apt-get install proxysql percona-xtradb-cluster-client-5.7
```


edit /etc/proxysql-admin.cnf to look like the following

```
# proxysql admin interface credentials.
export PROXYSQL_USERNAME="admin"
export PROXYSQL_PASSWORD="admin"
export PROXYSQL_HOSTNAME="dbrouter"
export PROXYSQL_PORT="6032"

# PXC admin credentials for connecting to pxc-cluster-node.
export CLUSTER_USERNAME="proxysql"
export CLUSTER_PASSWORD="<proxy_password>"
export CLUSTER_HOSTNAME="172.28.0.10"
export CLUSTER_PORT="3306"

# proxysql monitoring user. proxysql admin script will create this user in pxc to monitor pxc-nodes.
export MONITOR_USERNAME="monitor"
export MONITOR_PASSWORD="<montior_password>"

# Application user to connect to pxc-node through proxysql
export CLUSTER_APP_USERNAME="proxysql_user"
export CLUSTER_APP_PASSWORD="<proxyuser_password>"

# ProxySQL read/write hostgroup
export WRITE_HOSTGROUP_ID="10"
export READ_HOSTGROUP_ID="11"

# ProxySQL read/write configuration mode.
export MODE="singlewrite"

# ProxySQL Cluster Node Priority File
export HOST_PRIORITY_FILE="/var/lib/proxysql/host_priority.conf"
```


## Initialize ProxySQL:

`sudo proxysql-admin --config-file=/etc/proxysql-admin.cnf --enable --syncusers`



## Check ProxySQL Config:

`mysql -u admin -padmin -h 127.0.0.1 -P 6032`

Ensure all DB Servers show online.  

```
mysql> SELECT hostname,port,status,comment FROM mysql_servers;
+-------------+------+--------+---------+
| hostname    | port | status | comment |
+-------------+------+--------+---------+
| 172.28.0.20 | 3306 | ONLINE | READ    |
| 172.28.0.30 | 3306 | ONLINE | READ    |
| 172.28.0.10 | 3306 | ONLINE | WRITE   |
+-------------+------+--------+---------+
3 rows in set (0.00 sec)

mysql>
```


## To Change the ProxySQL Admin password:

`mysql -u admin -padmin -h 127.0.0.1 -P 6032`

Verify the old password:
`SELECT * FROM global_variables where variable_name = 'admin-admin_credentials';`

Update to the new password:
```
UPDATE global_variables SET variable_value='admin:<password>' WHERE variable_name='admin-admin_credentials';
SAVE ADMIN VARIABLES TO DISK;
LOAD ADMIN VARIABLES TO RUNTIME;
```

Verify the new password:
`SELECT * FROM global_variables where variable_name = 'admin-admin_credentials';`

Test login with

`mysql -u admin -p<password> -h 127.0.0.1 -P 6032`

Make sure to update the admin password in /etc/proxysql-admin.cnf in case Proxysql-admin needs to be re-ran.



## To refresh Proxy SQL if new users get added:

```
sudo proxysql-admin --config-file=/etc/proxysql-admin.cnf --disable
sudo proxysql-admin --config-file=/etc/proxysql-admin.cnf --enable --syncusers
```

