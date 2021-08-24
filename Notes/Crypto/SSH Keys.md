---
tags: [Crypto, Linux]
title: SSH_Keys
created: '2020-01-30T20:16:15.923Z'
modified: '2020-07-08T13:00:29.593Z'
---

# SSH Keys

RSA Private Key stored in `~./ssh/id_rsa`
RSA Public Key stored in `~./ssh/id_rsa.pub`

### To generate a new key run
```bash
ssh-keygen -t rsa
```

### To SSH in to a remote system without a password:
Copy the data in the pub file to a new line in  `~/.ssh/authorized_keys` on the remote system. 
Make sure .ssh/ and authorized_keys is owned by the username, and group is the username
Run chmod 700 on `~/.ssh` and chmod 600 on authorized_keys file.

Comments can be added to authorized_keys file on lines starting with #

You can also use the `ssh-copy-id` command to automatically do this. 
By default the linux command ssh-copy-id will look for the public key in 
`~/.ssh/identity.pub`

By defualt the linux ssh client will use `~/.ssh/id_rsa` as a private key.

### To get the public key from the private key run 
```bash
ssh-keygen -y -f ~/.ssh/id_rsa
```

### To manually point SSH to a private key to use  
```bash
ssh -i ~/.ssh/id_rsa.otherkey user@server.example.com
```

## Managing multiple keys and hosts with config file. 

It’s good to have many keys, e.g. one for GitHub, one for BitBucket, one for your server. But, by default the id_rsa.pub file is always used, we have to tell ssh to look different public key file depending on the service. This is where the config files come in.

config file lives in `~/.ssh/config`, if it’s not there, go ahead and make it.

It should look like 
```
Host           myserver
  HostName       192.168.1.1
  Port           5555
  IdentityFile   ~/.ssh/company/id_rsa
  User           sysadmin

Host           mysecondserver
  HostName       192.168.1.3
  IdentityFile   ~/.ssh/company2/id_rsa
  User           sysadmin

Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/_id_rsa
  IdentitiesOnly yes
```

Then you can connect with `ssh myserver`

Managing multiple Git Repos

```
Host github-corporate
        HostName github.com
        User git
        IdentityFile ~/.ssh/id_rsa_corp
        IdentitiesOnly yes
```
use `git clone git@github-corporate:company/project.git`

```
Host github-personal
        HostName github.com
        User git
        IdentityFile ~/.ssh/id_rsa_personal
        IdentitiesOnly yes
```
use `git clone git@github-personal:steve/other-pi-project.git`
