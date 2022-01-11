Reference: https://trstringer.com/cheap-kubernetes-in-azure/

##  Create the Cluster

```
az group create --location eastus2 --name <resource_group>

az aks create \
    --resource-group <resource_group> \
    --name <aks> \
    --location eastus2 \
    --node-count 1 \
    --node-vm-size "Standard_B2s"
```


## Stop the cluster

```
az aks stop \
--resource-group <resource_group> \
--name <aks>
```

Notes on load balancer: https://trstringer.com/cheap-aks-load-balancer/