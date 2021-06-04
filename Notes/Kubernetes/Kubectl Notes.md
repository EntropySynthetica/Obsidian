# kubectl Notes

#kubernetes

The Kubernetes command-line tool, kubectl, allows you to run commands against Kubernetes clusters. You can use kubectl to deploy applications, inspect and manage cluster resources, and view logs. For a complete list of kubectl operations, see https://kubernetes.io/docs/reference/kubectl/overview/

## Install kubectl manually
Download the latest kubectl
```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetesrelease/release/stable.txt`/bin/linux/amd64/kubectl
```

or grab a specific version. Make sure the version of Kubectl you run is same or newer than your Kubernetes cluster. 
`curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.17.0/bin/linux/amd64/kubectl`

make the kubectl binary executable
`chmod +x ./kubectl`

Move it to /usr/local/bin
`sudo mv ./kubectl /usr/local/bin/kubectl`

Check what version of kubectl you have
`kubectl version --client`

Check what version of kubectl and the version of k8s you are managing
`kubectl version`

## Install kubectl via APT on Ubuntu
```bash
sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

## kubectl config file

The Kubectl config file contains the location and credentials to manage your kubernetes cluster.  On install most kubernetes installers will provide you with a kubeconfig file to provide to kubectl. 

By Default kubectl looks for a config in `~/.kube/config`

It will also look for a config file in the enviromental variable $KUBECONFIG

You can set the filename to lookfor in ~/.kube/ with the following command
`export KUBECONFIG=config.clustername.yaml`

Multiple sites can be chained together seperated by a :
`export KUBECONFIG=$KUBECONFIG:config.site1.yaml:config.site2.yaml`

Bash script to automatically add any .yaml files in the .kube directory into the KUBECONFIG enviromental variable.

```bash
for f in `ls ~/.kube/ | grep config.\*.yaml`
do 
    export KUBECONFIG="$HOME/.kube/$f:$KUBECONFIG"; 
done
```

## Optional Install kubecontext and kubens
kubecontext is a bash script that gives a quicky way to change context
kubens is a bash script that gives a quick way to change namespace
Repo at: https://github.com/ahmetb/kubectx

```bash
sudo git clone https://github.com/ahmetb/kubectx /opt/kubectx
sudo ln -s /opt/kubectx/kubectx /usr/local/bin/kubecontext
sudo ln -s /opt/kubectx/kubens /usr/local/bin/kubens
```

Optional Install the package fzf to get kubecontext and kubens to be interactive
`sudo apt install fzf`

Add Alias for kubectl
`alias kc=kubectl`

## Install KubeCTL krew
Krew itself is a kubectl plugin that is installed and updated via Krew (yes, Krew self-hosts).

Run this command in your terminal to download and install krew:
```bash
(
  set -x; cd "$(mktemp -d)" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/krew.tar.gz" &&
  tar zxvf krew.tar.gz &&
  KREW=./krew-"$(uname | tr '[:upper:]' '[:lower:]')_$(uname -m | sed -e 's/x86_64/amd64/' -e 's/arm.*$/arm/')" &&
  "$KREW" install krew
)
```

Add $HOME/.krew/bin directory to your PATH environment variable. To do this, update your .bashrc or .zshrc file and append the following line:
```bash
export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"
```

## Install KubeCTL Neat
Remove clutter from Kubernetes manifests to make them more readable.

Install with:
`kubectl krew install neat`

Use: 
`kubectl get pod mypod -o yaml | kubectl neat`

## kubectl command examples

Check cluster info
`kubectl cluster-info`

checking and changing kubectl contexts
`kubectl tracks multiple clusters with a context name.  The name of these can be found and set in the config.sitename.yaml files.  `

show all contexts
`kubectl config get-contexts`

show current set context
`kubectl config current-context`

change current context
`kubectl config set-context <context_name>`

Get Namespaces in a Cluster
`kubectl get all --all-namespaces`

Set Default Namespace
`kubectl config set-context --current --namespace=<insert-namespace-name-here>`

Show Default Namespace
`kubectl config view --minify | grep namespace:`

### Examples that deploy a workload.
`kubectl create deployment nginx --image=nginx:1.14`

or
`kubectl create deployment helloworld --image=rancher/hello-world`

or
`kubectl create deployment demo --image=monachus/rancher-demo:latest`

### Upgrade the image a deployment uses
`kubectl set image deploy nginx nginx=nginx:1.15 --record`

the --record in the previous command allows rollback. 

Verify with
`kubectl get pods`

or
`kubectl get all`

### Expose workload via a Nodeport service
`kubectl create service nodeport helloworld --node-port=31337 --tcp=8080:8080`

Verify endpoint
`k3s kubectl get endpoints mynginx`

### Scale deployments
`kubectl scale deployments/helloworld --replicas=4`

### Show what nodes the pods are on with
`kubectl get pods -o wide`

### Check resources
See node utilization
`kubectl top node`

check pod cpu utilization
`kubectl top pod --all-namespaces`

### Get YAML for a deployment
`kubectl get deploy <deployment name> -o yaml`

### Get logs for a pod
`kubectl logs -f <pod name>`

### Get events for a pod (useful for troubleshooting)
`kubectl describe pod <pod name>`

## Getting logs

`kubectl logs -f` # stream logs
`kubectl logs --since=1h` # return logs newer than a relative duration
`kubectl logs --since-time=2020-08-13T10:46:00.000000000Z` # return logs after a specific date (RFC3339)
`kubectl logs --previous` # print the logs for the previous instance of the container
`kubectl logs -c` # print the logs of this container
`kubectl logs -l` #  print logs from all containers in pods defined by label
`kubectl get events --sort-by=’.metadata.creationTimestamp’` # print all events in chronological order
`kubectl describe pod`  # print pod details like status or recent events


## Install SSL Cert as Secret
`kubectl create secret tls ${CERT_NAME} --key ${KEY_FILE} --cert ${CERT_FILE}`

Create a manifest file with your ssl cert from pem format
`kubectl -n ingress-nginx create secret tls ingress-default-cert --cert=cert.pem --key=key.pem -o yaml --dry-run=true > ingress-default-cert.yaml`

Verify the expiration date of a secret in K8S
`kubectl get secret secretname -o "jsonpath={.data['tls\.crt']}" | base64 -d | openssl x509 -enddate -noout`

Verify the Cert details
`kubectl get secret ecwildcardcert -o "jsonpath={.data['tls\.crt']}" | base64 -d | openssl x509 -text -noout`

The cert can then be referneced by the ingress controller to enable HTTPS. 

## Cluster Maint

### Drain nodes from cluster to preform maintenance.

Get Node Names
`kubectl get nodes`

Drain the node
`kubectl drain <node name>`

Drain nodes with daeomon sets
`kubectl drain --ignore-daemonsets <node name>`

When maint is complete re-enable the node
`kubectl uncordon <node name>`

### Show Privilages 

Show my Current Privs in Kubectl
`kubectl auth can-i --list --namespace=kube-system`

## Useful Commands
Commands from this site: https://medium.com/flant-com/kubectl-commands-and-tips-7b33de0c5476

Get the list of nodes and their memory size
```
kubectl get no -o json | \
  jq -r '.items | sort_by(.status.capacity.memory)[]|[.metadata.name,.status.capacity.memory]| @tsv'
```

Get a list of nodes and the number of pods running on them
```
kubectl get po -o json --all-namespaces | \
  jq '.items | group_by(.spec.nodeName) | map({"nodeName": .[0].spec.nodeName, "count": length}) | sort_by(.count)'
```

Get a list of all pods in a cluster
```
kubectl get pods --all-namespaces
```

Get a list of non running pods
```
kubectl get pods -A --field-selector=status.phase!=Running | grep -v Complete
```

Copy secrets from one namespace to another
```
kubectl get secrets -o json --namespace namespace-old | \
  jq '.items[].metadata.namespace = "namespace-new"' | \
  kubectl create-f  -
```
or
```
kubectl get secret my-secret --namespace namespace1 -o yaml | sed "/namespace:/d" | kubectl apply --namespace=namespace2 -f -
```


Start a shell in a temporary Pod
```
# Ubuntu
kubectl run my-ubuntu --rm -i -t --restart=Never --image ubuntu -- bash

# CentOS
kubectl run my-centos --rm -i -t --restart=Never --image centos:8 -- bash

# Alpine
kubectl run my-alpine --rm -i -t --restart=Never --image alpine:3.10 -- sh

# Busybox
kubectl run my-busybox --rm -i -t --restart=Never --image busybox -- sh
```

Extract and Decode a Secret
```
kubectl get secret -n namespace1 my-postgresql -o jsonpath="{.data.postgres-password}" | base64 --decode
```