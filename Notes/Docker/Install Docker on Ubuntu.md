---
tags: [Docker, Kubernetes, Linux]
title: Install Docker on Ubuntu
created: '2020-01-30T18:19:31.299Z'
modified: '2020-02-02T18:07:08.287Z'
---

# Install Docker on Ubuntu


### Install required packages
```
sudo apt-get install \
	apt-transport-https \
	ca-certificates \
	curl \
	gnupg-agent \
	software-properties-common
```


### Get the Docker GPG key and add it to apt
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`

### Add the Docker Repo
```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

`sudo apt-get update`

### Install Docker
`sudo apt install docker-ce`

### Install a specific version of Docker and prevent it from upgrading

```
sudo apt-get install -y docker-ce=18.06.1~ce~3-0~ubuntu
sudo apt-mark hold docker-ce
```


### Add your user to docker
`sudo usermod -aG docker your-user`


## Install via auto install script

```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```


## Steps required if installing the offical Kube binary

### Setup daemon.
```
cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
	"max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
```

`mkdir -p /etc/systemd/system/docker.service.d`

### Restart docker.

```
systemctl daemon-reload
systemctl restart docker
```
