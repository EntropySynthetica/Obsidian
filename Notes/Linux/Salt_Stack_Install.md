---
tags: [Linux]
title: Salt_Stack_Install
created: '2020-01-30T20:16:15.883Z'
modified: '2020-01-30T20:52:33.983Z'
---

# Salt Stack Install
Created Tuesday 22 January 2019

Install Salt Master:
curl -L <https://bootstrap.saltstack.com> -o install_salt.sh

sudo sh install_salt.sh -P -M

The salt masters hostname should be salt. By default salt-minions try to connect to a host named salt.  

Install Salt Minion:

Minions need an [/etc/hosts](file:///etc/hosts) entry that resolves the name salt to the ip of the master. 

curl -L <https://bootstrap.saltstack.com> -o install_salt.sh

sudo sh install_salt.sh -P

Let's first take a look at what Salt keys we have available:
sudo salt-key -L

Let's now view the master fingerprint for the server:
sudo salt-key -F master

Note the master.pub key

On the Minions

sudo vi [/etc/salt/minion](file:///etc/salt/minion)

Find the line master_finger.  Uncomment it and add the master.pub key

restart the minion:
sudo service salt-minion restart

View the fingerprint for the minion:
sudo salt-call --local key.finger

On master except all keys:
sudo salt-key -A


*****

Running Salt Commands

Ping servers:
A wildcard can be passed to exectue on everything.
The name of a specific server or regex can be passed here. 

sudo salt '*' test.ping

Run the ping command by using Grains.  Grains contain tags that apply to certain systems. 
sudo salt -G 'os:Ubuntu' test.ping

Install a package
sudo salt '*' pkg.install htop

Show avaible upgrades
sudo salt '*' pkg.list_upgrades

Update apt cache
sudo salt '*' pkg.refresh_db

Do dist upgrade
sudo salt '*' pkg.upgrade dist_upgrade=True

Autoremove old packages
sudo salt '*' pkg.autoremove






