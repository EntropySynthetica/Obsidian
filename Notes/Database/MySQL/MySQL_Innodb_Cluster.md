---
tags: [Database, Linux, MySQL]
title: MySQL_Innodb_Cluster
created: '2020-01-30T20:16:15.815Z'
modified: '2020-02-27T21:57:08.716Z'
---

# MySQL Innodb Cluster
Created Monday 22 January 2018


In my lab there are 3 DB Servers. The IPs are as follows,

172.28.0.10 = db001
172.28.0.20 = db002
172.28.0.30 = db003

Do the following on all 3 servers. 

edit /etc/hosts

add an entry for each server including itself and you MUST remove the default entry that maps the server name to localhost.

The file should look like this,

----

#127.0.0.1      db001   db001
127.0.0.1       localhost

172.28.0.10     db001
172.28.0.20     db002
172.28.0.30     db003

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost   ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts
127.0.1.1       ubuntu-xenial   ubuntu-xenial


----

Next install the APT repo for MYSQL

sudo wget <http://dev.mysql.com/get/mysql-apt-config_0.8.4-1_all.deb>
sudo dpkg -i mysql-apt-config_0.8.4-1_all.deb

During the install select a prompt will come up. 
Select the first option, "MySQL Server and Cluster"
Select mysql-cluster-7.5
Select ok

sudo apt update

Next install mysql mysql-shell and Python

sudo apt install mysql-server mysql-shell python

During the install you will be asked to set a root MySQL root password during this.  Do so and note the password.

Open the mysql shell with the following,

sudo mysqlsh

There is a command that will auto configure our instance to be a cluster and make an account for us.  Run the following,

dba.configureLocalInstance();

You will be asked for the root password, enter the password you set earlier. 
Answer yes to the prompt asking if you want to modify mysqld.cnf
Select option 2, Create account with different name
For account name enter: remote@%
Enter the desired password for the account

exit mysql shell with \q

Restart mysql with
sudo service mysql restart

Make sure these steps are run on all database servers.  

From this point  forward (except for one step at the end) everything is done only on db001.

Go back into mysql shell
sudo mysqlsh

Next we will create quick shortcuts to the servers to use later on.  For reasons I have not figured out yet we can't use the 
hostname for db001, we must use the ip address. If we don't we will get an error later.  

Run the following commands,

var i1='[remote@172.28.0.10](mailto:remote@172.28.0.10):3306'
var i2='remote@db002:3306'
var i3='remote@db003:3306'

Next we test each server to make sure setup went ok. 

Run the following,

dba.checkInstanceConfiguration(i1)

You will get asked for the password you created for the remote username.

The reply should look like this,

mysql-js> dba.checkInstanceConfiguration(i1)
Please provide the password for '[remote@172.28.0.10](mailto:remote@172.28.0.10):3306': 
Validating instance...

The instance '172.28.0.10:3306' is valid for Cluster usage
{
"status": "ok"
}
mysql-js> 

Do the same for dba.checkInstanceConfiguration(i2) and dba.checkInstanceConfiguration(i3)
both should come back with the same results.  If they do procede, if not something got missed earlier. 

Next lets create the cluster and add the slave nodes,

Run the following,
shell.connect(i1)

You will be prompted for the password for the username remote

Create the cluster with the following,

var cluster=dba.createCluster('DevCluster')

We should get the following reply,

mysql-js> var cluster=dba.createCluster('DevCluster')
A new InnoDB cluster will be created on instance '[remote@172.28.0.10](mailto:remote@172.28.0.10):3306'.

Creating InnoDB cluster 'DevCluster' on '[remote@172.28.0.10](mailto:remote@172.28.0.10):3306'...
Adding Seed Instance...

Cluster successfully created. Use Cluster.addInstance() to add MySQL instances.
At least 3 instances are needed for the cluster to be able to withstand up to
one server failure.

mysql-js> 

We can check the cluster status with,
cluster.status()

The reply should look like this,

mysql-js> cluster.status()
{
"clusterName": "DevCluster", 
"defaultReplicaSet": {
"name": "default", 
"primary": "172.28.0.10:3306", 
"status": "OK_NO_TOLERANCE", 
"statusText": "Cluster is NOT tolerant to any failures.", 
"topology": {
"172.28.0.10:3306": {
"address": "172.28.0.10:3306", 
"mode": "R/W", 
"readReplicas": {}, 
"role": "HA", 
"status": "ONLINE"
}
}
}
}
mysql-js> 

Next we can test the slaves one last time to make srue they are ready,

Run the following,
cluster.checkInstanceState(i2)

The reply should look like the following,

mysql-js> cluster.checkInstanceState(i2)
Please provide the password for 'remote@db002:3306': 
Analyzing the instance replication state...

The instance 'db002:3306' is valid for the cluster.
The instance is new to Group Replication.

{
"reason": "new", 
"state": "ok"
}

Do the same for cluster.checkInstanceState(i3)

If everything looks good we will now add them.  Run the following,

cluster.addInstance(i2)

We are asked for our password and should get the following reply,

mysql-js> cluster.addInstance(i2)
A new instance will be added to the InnoDB cluster. Depending on the amount of
data on the cluster this might take from a few seconds to several hours.

Please provide the password for 'remote@db002:3306': 
Adding instance to the cluster ...

The instance 'remote@db002:3306' was successfully added to the cluster.

Add the next slave with,
cluster.addInstance(i3)

Now check the cluster status,
cluster.status()

The reply should look like this,

mysql-js> cluster.status()
{
"clusterName": "DevCluster", 
"defaultReplicaSet": {
"name": "default", 
"primary": "172.28.0.10:3306", 
"status": "OK", 
"statusText": "Cluster is ONLINE and can tolerate up to ONE failure.", 
"topology": {
"172.28.0.10:3306": {
"address": "172.28.0.10:3306", 
"mode": "R/W", 
"readReplicas": {}, 
"role": "HA", 
"status": "ONLINE"
}, 
"db002:3306": {
"address": "db002:3306", 
"mode": "R/O", 
"readReplicas": {}, 
"role": "HA", 
"status": "ONLINE"
}, 
"db003:3306": {
"address": "db003:3306", 
"mode": "R/O", 
"readReplicas": {}, 
"role": "HA", 
"status": "ONLINE"
}
}
}
}
mysql-js> 

If the reply does look like above everything is good to go.  

Now we need to save our settings so things persist thru a restart.  We need to do the following
on each db server,

enter mysql shell if you are not already,

sudo mysqlsh

Then run
dba.configureLocalInstance()

Enter the root mysql password when prompted,
Answer yes to the question asking if we can modify mysqld.cnf
Select option 3
If asked Do you want to disable super_read_only and continue? answer Y

Creating the cluster is now complete.  

Rebooting a db server should cause it to go missing then back to online in the cluster.status() page, proving the config survived a reboot.  

These notes were put together by piecing together the guides from the following pages,
<https://mysqlserverteam.com/mysql-innodb-cluster-real-world-cluster-tutorial-for-ubuntu-and-debian/>
and
<http://lefred.be/content/mysql-innodb-cluster-mysql-shell-starter-guide/>

The vagrantfile I used to create the VM's looks like this,

# Name of VM
VM_NAME = 'db001'

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"

  # Actual machine name
  config.vm.hostname = VM_NAME

  # Set VM name in Virtualbox
  config.vm.provider "virtualbox" do |v|
v.name = VM_NAME
v.memory = 512
  end

  #DHCP — comment this out if planning on using NAT instead
  #config.vm.network "private_network", type: "dhcp"

  #Static IP
  config.vm.network "private_network", ip: "172.28.0.10"

  # # Port forwarding — uncomment this to use NAT instead of DHCP
  # config.vm.network "forwarded_port", guest: 80, host: VM_PORT

  config.vm.provision "shell", inline: <<-SHELL
apt update
apt install screenfetch -y
  SHELL

end

Just adjust the VM_Name and ip for each of the servers.  

----
To check cluster status after logging out of mysqlsh

sudo mysqlsh
var i1='[remote@172.28.0.10](mailto:remote@172.28.0.10):3306'
var i2='remote@db002:3306'
var i3='remote@db003:3306'
shell.connect(i1)
var cluster=dba.getCluster('DevCluster')
cluster.status()

----
To recover from a reboot,

sudo mysqlsh

var i1='[remote@172.28.0.10](mailto:remote@172.28.0.10):3306'
var i2='remote@db002:3306'
var i3='remote@db003:3306'

shell.connect(i1)

var cluster = dba.rebootClusterFromCompleteOutage();

It may then be nessesary to reboot the slaves.  

check status with cluster.status()

----
Install MySQL Router

sudo wget <http://dev.mysql.com/get/mysql-apt-config_0.8.4-1_all.deb>
sudo dpkg -i mysql-apt-config_0.8.4-1_all.deb

sudo apt-get install mysql-router

mysqlrouter --bootstrap remote@db001:3306 --directory myrouter





