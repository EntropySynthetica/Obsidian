ArgoCD Notes

## Upgrade ArgoCD

Make sure Kubectl is connected to the cluster Argo is installed on and namespace is switched to ArgoCD

```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```


## Login to Cluster

```bash
argocd login <argo URL>
```

## Add External Cluster to Argo
You need to have the cluster context working in kubectl

Get the clusters context name from kubectl.  

```bash
kubectl config get-contexts
```

* Add the Cluster to ArgoCD

```bash
argocd cluster add <context-name>
```

* If the cluster already exists in Argo and the creds changed the cluster will need to be removed and re-added

```bash
argocd cluster rm <context-name>
```

* Show Clusters in Argo
```bash
argocd cluster list
```