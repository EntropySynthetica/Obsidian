---
tags: [Linux]
title: Apt resolve pub key not found
created: '2021-04-16T18:08:27.684Z'
modified: '2021-04-16T18:09:52.951Z'
---

# Apt resolve pub key not found

When running apt update the following error may come up for a 3rd party repo.

```
Hit:1 http://us.archive.ubuntu.com/ubuntu focal InRelease
Hit:2 http://us.archive.ubuntu.com/ubuntu focal-updates InRelease
Hit:4 http://ppa.launchpad.net/ansible/ansible/ubuntu bionic InRelease
Hit:5 https://download.docker.com/linux/ubuntu focal InRelease
Hit:6 http://us.archive.ubuntu.com/ubuntu focal-backports InRelease
Hit:7 http://us.archive.ubuntu.com/ubuntu focal-security InRelease
Hit:8 http://ppa.launchpad.net/certbot/certbot/ubuntu bionic InRelease
Hit:9 http://ppa.launchpad.net/deadsnakes/ppa/ubuntu focal InRelease
Get:3 https://packages.cloud.google.com/apt kubernetes-xenial InRelease [9,383 B]
Hit:10 http://ppa.launchpad.net/duplicity-team/duplicity-release-git/ubuntu focal InRelease
Hit:11 http://ppa.launchpad.net/git-core/ppa/ubuntu focal InRelease
Err:3 https://packages.cloud.google.com/apt kubernetes-xenial InRelease
  The following signatures couldn't be verified because the public key is not available: NO_PUBKEY FEEA9169307EA071 NO_PUBKEY 8B57C5C2836F4BEB
Reading package lists... Done
Building dependency tree
Reading state information... Done
All packages are up to date.
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: https://packages.cloud.google.com/apt kubernetes-xenial InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY FEEA9169307EA071 NO_PUBKEY 8B57C5C2836F4BEB
W: Failed to fetch https://apt.kubernetes.io/dists/kubernetes-xenial/InRelease  The following signatures couldn't be verified because the public key is not available: NO_PUBKEY FEEA9169307EA071 NO_PUBKEY 8B57C5C2836F4BEB
W: Some index files failed to download. They have been ignored, or old ones used instead.
```

To resolve 

```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys FEEA9169307EA071
```

Do this for each key that is in the error. 
