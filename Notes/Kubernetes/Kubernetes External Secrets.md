
## KES Install for Vault

Reference: https://medium.com/craftech/manage-your-kubernetes-secrets-with-hashicorp-vault-25d2fb8119f


Prerequisites 

* Hasicorp Vault setup somewhere and reachable via a http / https URL
* kubectl setup and talking to your cluster on your local system.
* The vault client on your local system

### Install KES
Create a namespace for KES
```bash
kubectl create namespace external-secrets
```


Add the KES Repo to Helm
```bash
helm repo add external-secrets https://external-secrets.github.io/kubernetes-external-secrets/
```


Install KES into the cluster

Download a copy of values.yaml from here, https://github.com/external-secrets/kubernetes-external-secrets/blob/master/charts/kubernetes-external-secrets/values.yaml

Edit the file and edit the line that looks like `VAULT_ADDR: http://127.0.0.1:8200` to have the URL for your vault server.  

Use Helm to install KES
```bash
helm install kubernetes-external-secrets external-secrets/kubernetes-external-secrets --namespace external-secrets --values values.yaml
```


### Connect Vault to KES

1. Setup your environmental variables for future commands to reference.  CLUSTER_ID will be the name of your cluster referenced in your kubectl config file. 

```bash
export CLUSTER_NAME=rke
export VAULT_ADDR=https://vault.entropycraft.com  
export VAULT_TOKEN=abcdefg123456 #Initial root token
export CLUSTER_ID=kubenode01
```

2. Apply a clusterrolebinding for the ExternalSecrets’ serviceaccount with the role _auth-delegator._

Save the following as clusterrolebinding.yaml
```YAML
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding  
metadata:  
  name: role-tokenreview-binding  
  namespace: default  
roleRef:  
  apiGroup: rbac.authorization.k8s.io  
  kind: ClusterRole  
  name: system:auth-delegator  
subjects:  
- kind: ServiceAccount  
  name: kubernetes-external-secrets  
  namespace: external-secrets  
```
Apply the cluster role binding with `kubectl apply -f clusterrolebinding.yaml`

3. Create a policy in Vault for the service accounts token to be attached to.
```bash
vault policy write ${CLUSTER_NAME}-kv-rw - <<EOF
path "kv/data/${CLUSTER_NAME}/*" {  
capabilities = ["create", "read", "update", "delete", "list"]  
}  
EOF
```

4. Extract K8S credentials into environmental vars

Extract the name of the secret for the KES service account
```bash
SECRET_NAME="$(kubectl get serviceaccount kubernetes-external-secrets -o go-template='{{ (index .secrets 0).name }}')"
```

Extract the Token from the KES service account
```bash
SERVICEACCOUNT_TOKEN="$(kubectl get secret ${SECRET_NAME} -o go-template='{{ .data.token }}' | base64 --decode)"
```

Extract the Hostname of your cluster from your kubeconfig file
```bash
K8S_HOST="$(kubectl config view --raw -o go-template="{{ range .clusters }}{{ if eq .name \"${CLUSTER_ID}\" }}{{ index .cluster \"server\" }}{{ end }}{{ end }}")"
```

Extract your CACert for your cluster from your kubeconfig file
```bash
K8S_CACERT="$(kubectl config view --raw -o go-template="{{ range .clusters }}{{ if eq .name \"${CLUSTER_ID}\" }}{{ index .cluster \"certificate-authority-data\" }}{{ end }}{{ end }}" | base64 --decode)"
```

5. Enable Kubernetes Auth Method in Vault
```bash
vault auth enable --path="${CLUSTER_NAME}" kubernetes
```

6. Configure the K8S auth method with the creds you extracted above. 
```bash
vault write auth/${CLUSTER_NAME}/config kubernetes_host="${K8S_HOST}" kubernetes_ca_cert="${K8S_CACERT}" token_reviewer_jwt="${SERVICEACCOUNT_TOKEN}"
```

7. Create a role in the vault K8S auth method that references the KES service account and the policy in vault to apply

```bash
vault write auth/${CLUSTER_NAME}/role/${CLUSTER_NAME}-role bound_service_account_names="kubernetes-external-secrets" bound_service_account_namespaces="external-secrets" policies="default,${CLUSTER_NAME}-kv-rw" ttl="15m"
```

### Injecting a secret from the vault

Save the following as externalsecret.yaml
```YAML
apiVersion: 'kubernetes-client.io/v1'
kind: ExternalSecret
metadata:
  name: test
  namespace: default
spec:
  backendType: vault
  vaultMountPoint: rke #created at step 5
  vaultRole: rke-role #created at step 7
  dataFrom:
    - kv/data/rke/test
```

Verify the secret with `kubectl get externalsecret test`

You should get a Error 404 because the secret has not been created yet.   If you get a token error something is wrong with the vaultMountPoint, or a above auth step.  If you get a permission error something is wrong with the vaultRole or the path in the dataFrom.  

If it doesn't already exist you will need to enable the KV secrets engine. 

```bash
vault secrets enable kv
```

You can create data in vault with
```bash
vault kv put kv/rke/test key1="value" key2="value2"
```






## KES Install AKS
