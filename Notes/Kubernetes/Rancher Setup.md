---
tags: [Kubernetes]
title: Rancher_Setup
created: '2020-01-30T20:16:15.731Z'
modified: '2021-03-11T14:44:35.157Z'
---

# Rancher Setup
Created Tuesday 29 October 2019


To disable ubuntu’s default dns server to free up port 53, I ended up doing the following

Add ‘127.0.0.1 localhost’ to /etc/hosts file
update /etc/resolv.conf to only include:
```console
nameserver 8.8.8.8
nameserver 8.8.4.4
```

Run the following:
```bash
sudo systemctl disable systemd-resolved.service
sudo service systemd-resolved stop
```

rancher/hello-world

## Startup rancher with self signed certs
```bash
sudo docker run -d --restart=unless-stopped -p 80:80 -p 443:443 -v /opt/rancher:/var/lib/rancher --privileged rancher/rancher:v2.7.1
```

## Startup Rancher with proper certs
```bash
docker run -d --restart=unless-stopped \
  -p 80:80 -p 443:443 \
  -v /opt/rancher:/var/lib/rancher \
  -v /opt/certs/cert.pem:/etc/rancher/ssl/cert.pem \
  -v /opt/certs/key.pem:/etc/rancher/ssl/key.pem \
  -v /opt/certs/cacert.pem:/etc/rancher/ssl/cacerts.pem --privileged \
  rancher/rancher:v2.7.1
```

## Get the go daddy intermediate cert
wget <https://certs.godaddy.com/repository/gd_bundle.crt>




