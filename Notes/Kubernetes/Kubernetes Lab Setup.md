---
attachments: [components-of-kubernetes.png]
tags: [Kubernetes]
title: Kubernetes Lab Setup
created: '2020-08-26T16:12:04.379Z'
modified: '2020-09-10T18:51:51.420Z'
---

# Kubernetes Lab Setup

The following will detail creating a Kubernetes lab and setting up a test workload

Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation. It has a large, rapidly growing ecosystem. Kubernetes services, support, and tools are widely available. It was developed by Google using the Go Programming Language.

## Requirements

* Ubuntu Linux 16.04 or newer with internet access.
* Firewall open TCP ports 80, 443, 6443, and 30000 to 32600

## What will be installed?

* Kubernetes will be installed via K3S.
* Docker will be used as a container runtime.
* kubectl will be used to manage the node.

## Kubernetes Components

* **kube-apiserver**  - The API server is a component of the Kubernetes control plane that exposes the Kubernetes API. The API server is the front end for the Kubernetes control plane.
* **etcd**  -  Consistent and highly-available key value store used as Kubernetes' backing store for all cluster data.  In this lab a SQLite Database will take the place of etcd. 
* **kube-scheduler**  - Control plane component that watches for newly created Pods with no assigned node, and selects a node for them to run on. Factors taken into account for scheduling decisions include: individual and collective resource requirements, hardware/software/policy constraints, affinity and anti-affinity specifications, data locality, inter-workload interference, and deadlines.
* **kube-controller-manager**  - Control Plane component that runs controller processes. Logically, each controller is a separate process, but to reduce complexity, they are all compiled into a single binary and run in a single process.
* **kubelet**  - An agent that runs on each node in the cluster. It makes sure that containers are running in a Pod. The kubelet takes a set of PodSpecs that are provided through various mechanisms and ensures that the containers described in those PodSpecs are running and healthy. The kubelet doesn’t manage containers which were not created by Kubernetes.
* **kube-proxy**  - maintains network rules on nodes. These network rules allow network communication to your Pods from network sessions inside or outside of your cluster.


![components-of-kubernetes](../attachments/components-of-kubernetes.png)

## Install Prerequisite packages. 

```
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```

## Install Docker

### Get the Docker GPG key and add it to apt
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

### Add the Docker Repo
```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

### Install Docker
```
sudo apt update
```

```
sudo apt install docker-ce
```

## Verify Docker is running

### Add your user to docker
```
sudo usermod -aG docker your-user
```

You will need to log out and back in after adding yourself to the group. 

Check Docker Version
```
docker --version
```

Show running Containers
```
docker ps -a
```

[Additional Docker Install Notes](Install Docker on Ubuntu.md)

## Install Kubectl

### Add Google Kubernetes GPG key
```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

### Add the Google Kubernetes Repo
```
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
```

### Install kubectl
```
sudo apt update
```

```
sudo apt install kubectl
```

### Verify kubectl works
```
kubectl version --client
```

[Additional kubectl Notes](kubectl Notes.md)


## Install K3S

Download the K3s Binary, the links can be found at https://github.com/rancher/k3s/releases/latest
example:

```
curl -OL https://github.com/rancher/k3s/releases/download/v1.18.8%2Bk3s1/k3s
```

Move K3S to the proper directory
```
chmod +x k3s 
```

```
sudo mv k3s /usr/local/bin
```

Test with 
```
k3s --version
```

Start K3s with 

```
sudo k3s server --docker &>/dev/null &
```

[Additional k3s Notes](K3S Notes.md)

## Setup kubectl to talk to the cluster

Once K3S is up and running it will generate a config file at /etc/rancher/k3s/k3s.yaml
To get kubectl to talk to k3s we need to feed this config file to kubectl.  

Switch to your home directory

```
cd ~
```

Make a directory named .kube

```
mkdir .kube
```

Copy the kube config file into the .kube folder 

```
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
```

fixup the permissions on the config file


```
sudo chown sysadmin:sysadmin ~/.kube/config
```

Verify kubectl can see the cluster with 

```
kubectl get nodes
```

## Add a deployment to the Kubernetes cluster

Create a deployment using a test webserver.

```
kubectl create deployment demo --image=monachus/rancher-demo:latest
```

Verify the deployment is running

```
kubectl get deploy
```

## Expose the deployment using a service. 

Expose the deployment using a nodeport service

```
kubectl create service nodeport demo --node-port=31337 --tcp=8080:8080
```

Verify the Service exists

```
kubectl get service
```

Point your web browser to http://<node IP>:31337 to access the workload.

## Scale up the service. 

```
kubectl scale deployments/demo --replicas=4
```

## Add a second node to the cluster.  

Repeat the steps to install docker and download K3s on a second node.  

We need to grab the cluster auth token from the first node.  It can be found with the following command

```
sudo cat /var/lib/rancher/k3s/server/node-token
```

On the new node we will start K3S with this command

```
sudo k3s agent --docker --server ${K3S_URL} --token ${K3S_TOKEN} &>/dev/null &
```

To verify things are running correctly run `kubectl get nodes`.  You should now see 2 nodes in the cluster.

```
sysadmin@kubelab01:~$ kubectl get nodes
NAME        STATUS   ROLES    AGE   VERSION
kubelab01   Ready    master   36m   v1.18.8+k3s1
kubelab02   Ready    <none>   23s   v1.18.8+k3s1

```

## Managing the cluster with Rancher

Create a server to act as the rancher server. Install Docker on it using the steps outlined above.  

Verify docker is running with the following command

```
docker ps
```

Start rancher with the following command 

```
sudo docker run -d --restart=unless-stopped -p 80:80 -p 443:443 -v /opt/rancher:/var/lib/rancher rancher/rancher:v2.4.7
```

Rancher should start within a few minutes and can be accessed at https://<ip of rancher server>


When logging in for the first time Rancher will ask you to set the password for the admin account, and to enter the URL the server can be reached out by outside services.  


Once in the main rancher UI click the "Add Cluster" button

Select "Import an Existing Cluster"

Give your cluster a name and click create. 

Rancher will provide us with a kubectl command we need to run.  The command will create pods that allow the cluster to be managed by rancher.  
