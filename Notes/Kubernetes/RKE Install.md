---
tags: [Kubernetes]
title: RKE_Install
created: '2020-01-30T20:16:15.739Z'
modified: '2021-05-04T13:49:36.862Z'
---

# RKE Install

Rancher Kubernetes Engine (RKE) is a CNCF-certified Kubernetes distribution that runs entirely within Docker containers. It works on bare-metal and virtualized servers. RKE solves the problem of installation complexity, a common issue in the Kubernetes community. With RKE, the installation and operation of Kubernetes is both simplified and easily automated, and it’s entirely independent of the operating system and platform you’re running. As long as you can run a supported version of Docker, you can deploy and run Kubernetes with RKE.

## Kubectl install on any host that will manage the cluster
```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

To connect to a cluster get it's kubeconfig file and store it somewhere.  The default search path is ~/.kube/config
To use a differnt file run
set KUBECONFIG ./kube/config.<clustername](./RKE_Install/kube/config.<clustername)>.yml

## Install Docker on the nodes
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh
sudo usermod -aG docker your-user
```

## Install NFS on the nodes for remote persistent storage
```bash
sudo apt install nfs-common
```
Then check that the rpcbind service is started and enabled on boot.



## Get RKE binary installed on a server that is not one of the nodes. 
```bash
curl -LO https://github.com/rancher/rke/releases/download/v1.3.9/rke_linux-amd64
chmod +x rke_linux-amd64
sudo cp rke_linux-amd64 /usr/local/bin/rke
rke --version
```

## Check which versions of kube work with the version of RKE
`rke config --list-version --all`

## Make RKE config file.

rke config --name cluster.yml

```bash
erica@kubemaster:~$ rke config --name cluster.yml
[+] Cluster Level SSH Private Key Path [~/.ssh/id_rsa]: 
[+] Number of Hosts [1]: 2
[+] SSH Address of host (1) [none]: 192.168.68.101
[+] SSH Port of host (1) [22]: 
[+] SSH Private Key Path of host (192.168.68.101) [none]: ~/.ssh/id_rsa
[+] SSH User of host (192.168.68.101) [ubuntu]: erica
[+] Is host (192.168.68.101) a Control Plane host (y/n)? [y]: y
[+] Is host (192.168.68.101) a Worker host (y/n)? [n]: y
[+] Is host (192.168.68.101) an etcd host (y/n)? [n]: y
[+] Override Hostname of host (192.168.68.101) [none]: node01
[+] Internal IP of host (192.168.68.101) [none]: 
[+] Docker socket path on host (192.168.68.101) [/var/run/docker.sock]: 
[+] SSH Address of host (2) [none]: 192.168.68.102
[+] SSH Port of host (2) [22]: 
[+] SSH Private Key Path of host (192.168.68.102) [none]: ~/.ssh/id_rsa
[+] SSH User of host (192.168.68.102) [ubuntu]: erica
[+] Is host (192.168.68.102) a Control Plane host (y/n)? [y]: y
[+] Is host (192.168.68.102) a Worker host (y/n)? [n]: y
[+] Is host (192.168.68.102) an etcd host (y/n)? [n]: y
[+] Override Hostname of host (192.168.68.102) [none]: node02
[+] Internal IP of host (192.168.68.102) [none]: 
[+] Docker socket path on host (192.168.68.102) [/var/run/docker.sock]: 
[+] Network Plugin Type (flannel, calico, weave, canal) [canal]: 
[+] Authentication Strategy [x509]: 
[+] Authorization Mode (rbac, none) [rbac]: 
[+] Kubernetes Docker image [rancher/hyperkube:v1.16.3-rancher1]: 
[+] Cluster domain [cluster.local]: 
[+] Service Cluster IP Range [10.43.0.0/16]: 
[+] Enable PodSecurityPolicy [n]: 
[+] Cluster Network CIDR [10.42.0.0/16]: 
[+] Cluster DNS Service IP [10.43.0.10]: 
[+] Add addon manifest URLs or YAML files [no]: 
```

Save a copy of the following files in a secure location (they will be output by the tool):

cluster.yml: The RKE cluster configuration file.
kube_config_cluster.yml: The Kubeconfig file for the cluster, this file contains credentials for full access to the cluster.
cluster.rkestate: The Kubernetes Cluster State file, this file contains credentials for full access to the cluster.

## Start the cluster with
`rke up`

## Example cluster.yml file
```yaml
# If you intened to deploy Kubernetes in an air-gapped environment,
# please consult the documentation on how to configure custom RKE images.
nodes:
- address: 172.28.28.231
  port: "22"
  internal_address: ""
  role:
  - controlplane
  - worker
  - etcd
  hostname_override: "node01"
  user: erica
  docker_socket: /var/run/docker.sock
  ssh_key: ""
  ssh_key_path: ~/.ssh/id_rsa
  ssh_cert: ""
  ssh_cert_path: ""
  labels: {}
  taints: []

- address: 172.28.28.232
  port: "22"
  internal_address: ""
  role:
  - controlplane
  - worker
  - etcd
  hostname_override: "node02"
  user: erica
  docker_socket: /var/run/docker.sock
  ssh_key: ""
  ssh_key_path: ~/.ssh/id_rsa
  ssh_cert: ""
  ssh_cert_path: ""
  labels: {}
  taints: []

- address: 172.28.28.233
  port: "22"
  internal_address: ""
  role:
  - controlplane
  - worker
  - etcd
  hostname_override: "node03"
  user: erica
  docker_socket: /var/run/docker.sock
  ssh_key: ""
  ssh_key_path: ~/.ssh/id_rsa
  ssh_cert: ""
  ssh_cert_path: ""
  labels: {}
  taints: []

services:
  etcd:
    image: ""
    extra_args: {}
    extra_binds: []
    extra_env: []
    external_urls: []
    ca_cert: ""
    cert: ""
    key: ""
    path: ""
    uid: 0
    gid: 0
    snapshot: true
    retention: "24h"
    creation: "6h0s"
    backup_config: null
  kube-api:
    image: ""
    extra_args: {}
    extra_binds: []
    extra_env: []
    service_cluster_ip_range: 10.43.0.0/16
    service_node_port_range: ""
    pod_security_policy: false
    always_pull_images: false
    secrets_encryption_config: null
    audit_log: null
    admission_configuration: null
    event_rate_limit: null
  kube-controller:
    image: ""
    extra_args: {}
    extra_binds: []
    extra_env: []
    cluster_cidr: 10.42.0.0/16
    service_cluster_ip_range: 10.43.0.0/16
  scheduler:
    image: ""
    extra_args: {}
    extra_binds: []
    extra_env: []
  kubelet:
    image: ""
    extra_args: {}
    extra_binds: []
    extra_env: []
    cluster_domain: cluster.local
    infra_container_image: ""
    cluster_dns_server: 10.43.0.10
    fail_swap_on: false
    generate_serving_certificate: false
  kubeproxy:
    image: ""
    extra_args: {}
    extra_binds: []
    extra_env: []

network:
  plugin: canal
  options: {}
  mtu: 0
  node_selector: {}
  update_strategy: null

authentication:
  strategy: x509
  sans: []
  webhook: null

addons: ""
addons_include: []
ssh_key_path: ~/.ssh/id_rsa
ssh_cert_path: ""
ssh_agent_auth: false
authorization:
  mode: rbac
  options: {}
ignore_docker_version: false

kubernetes_version: "v1.21.4-rancher1-1"

private_registries: []

ingress:
  provider: ""
  options: {}
  node_selector: {}
  extra_args: {}
  dns_policy: ""
  extra_envs: []
  extra_volumes: []
  extra_volume_mounts: []
  update_strategy: null

cluster_name: ""
cloud_provider:
  name: ""

prefix_path: ""
addon_job_timeout: 0

bastion_host:
  address: ""
  port: ""
  user: ""
  ssh_key: ""
  ssh_key_path: ""
  ssh_cert: ""
  ssh_cert_path: ""
monitoring:
  provider: ""
  options: {}
  node_selector: {}
  update_strategy: null
  replicas: null
restore:
  restore: false
  snapshot_name: ""
dns: null
```


## Cluster Upgrades

Open cluster.yml and edit the line `kubernetes_version:` then run `rke up`
