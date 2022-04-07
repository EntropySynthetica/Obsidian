# Azure Vault External Secrets in K8S

## Setup Azure Keyvault

Azure Key Vault is a cloud service for securely storing and accessing secrets. A secret is anything that you want to tightly control access to, such as API keys, passwords, certificates, or cryptographic keys.

### Create the Vault

1. Log into the Azure Portal

2. Select Key Vaults

3. Click Create

4. Set the Subscription, Resource Group, Region and Name as appropriate

5. Click Next: Access Policy

6. Ensure Permission Model is set to Vault Access Policy

7. Click Review + create

### Create some Secrets

1. Click into the Vault name created previous

2. Click Secrets

3. Click Generate / Import

4. Set the name as the desired Key of the secret. 

5. Set the value to the data to store in the secret.  

6. All other settings can remain default. Click Create. 

7. Repeat for any additional secrets required.  

## Setup a Service Account for Azure Keyvault

### Setup the Service Account
After creating the Azure Key Vault we need to create an application account to access the Vault with.  We will create an App Registration in Azure Active Directory, then go to the vault to give that App Registration permissions. 

Reference Docs for App Registration: https://docs.microsoft.com/en-gb/azure/active-directory/develop/quickstart-register-app#to-add-application-credentials-or-permissions-to-access-web-apis

1. Log into the Azure Portal

2. Open Azure Active Directory

3. Open App Registrations

4. Click New Registration

5. 
    a. Give the Application a name that indicates it is related to the vault.

    b. Set the application access to This Organization directory only

    c. Redirect URI can be left blank

    d. Click Register

6. Back on the App Registration Screen click the name of the App created in the previous step. 

7. Copy the Application (Client) ID, and the Directory (Tenant) ID.  You will need these later.  

7. Click Certificates and Secrets.  

8. Select Client Secrets and click New Client Secret
Give the Secret a unique name, and set the expiration to a reasonable length
Click Add

9.  Copy the Value of the secret, you will need this later.  

### Give the account Permissions to access the Vault

1. Go back to the Azure Portal home Page

2. Go to Key Vaults

3. Click into the name of the vault we need to get access to. 

4. Click Access Policies.  

5. Click Add Access Policy

6. Under Key Permissions select (Get, List)

7. Under Secret Permissions select (Get, List)

8.  Under Certificate Permissions select (Get, List)

9. Click the None Selected next to Select Principal.  A search bar will show up.  Search for the name of the Application you setup in the previous section.  Select it.  

10. Click Add

## Install External Secrets Operator into K8S

External Secrets Operator is a Kubernetes operator that integrates external secret management systems like AWS Secrets Manager, HashiCorp Vault, Google Secrets Manager, Azure Key Vault and many more. The operator reads information from external APIs and automatically injects the values into a Kubernetes Secret.

Reference URL: https://external-secrets.io/


The External Secrets Operator is installed via Helm. 

1. Add the Helm Repo
```bash
helm repo add external-secrets https://charts.external-secrets.io
helm repo add emberstack https://emberstack.github.io/helm-charts
```

```
helm repo update
```

2. Install the Helm Chart

```bash
helm upgrade external-secrets \
   external-secrets/external-secrets \
    -n external-secrets \
    --create-namespace \
    --install

helm upgrade reflector \
   emberstack/reflector \
    -n reflector \
    --create-namespace \
    --install
```


## Sync External Secrets to Azure Vault

### 1. Switch to the namespace you want the secret to reside in.  

### 2. Create a K8S Secret that contains the creds for the Azure Key Vault. 

Option 1, use the Kubectl Command to create the secret.  
```
kubectl create secret generic azure-vault-creds \
   --from-literal=ClientID=<Azure Client ID> \ 
   --from-literal=ClientSecret=<Azure Client Secret> \
```

Option 2, Create a secret manifest file and apply it. 


vaultcreds.yml
```yaml
apiVersion: v1
kind: Secret
type: Opaque

metadata:
  name: azure-vault-creds

data:
  ClientID: <base64 client ID>
  ClientSecret: <base 64 client secret>

```

```
kubectl apply -f vaultcreds.yml
```

### 3. Add a SecretStore to K8S

The External Secrets Operator adds a new Kubernetes resource called a Secret Store.  This contains the info ESO needs to connect to the Azure Vault.  

The Secret Store is configured by applying a yaml file.

azure_secretstore.yml
```yaml
apiVersion: external-secrets.io/v1beta1
kind: SecretStore
metadata:
  name: <Name of vault>
spec:
  provider:
    # provider type: azure keyvault
    azurekv:
      # azure tenant ID, see: https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-how-to-find-tenant
      tenantId: "<tenant id>"

      # URL of your vault instance, see: https://docs.microsoft.com/en-us/azure/key-vault/general/about-keys-secrets-certificates
      vaultUrl: "<vault url>"
      
      authSecretRef:
        # points to the secret that contains
        # the azure service principal credentials
        clientId:
          name: azure-vault-creds
          key: ClientID
        clientSecret:
          name: azure-vault-creds
          key: ClientSecret

```

```
kubectl apply -f azure_secretstore.yml
```

### 4. Add a ExternalSecret to K8S

ESO adds Kubernetes resource called External Secret.  This tells Kubernetes what Key / Value pairs to grab and what secret name in Kubernetes to sync them to.  

An External Secret is setup with a Kubernetes Manifest file.

externalsecret.yml
```yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: aks-secret
spec:
  refreshInterval: 10m
  secretStoreRef:
    kind: SecretStore
    name: <Name of Vault>

  target:
    name: azure-secret # Name of the Secret in K8S to sync values to.  
    creationPolicy: Owner

  data:
  # explicit type and name of secret in the Azure KV
  - secretKey: <key 1 name>
    remoteRef:
      key: secret/<key 1 name>

  - secretKey: <key 2 name>
    remoteRef:
      key: secret/<key 2 name>

```

```
kubectl apply -f externalsecret.yml
```

The External Secrets Operator will now sync the values from the Azure Vault to the specified secret.  The secrets will be re-synced at the time interval specified in the external secret.  

## Certificate Management

### ExternalSecret Certificate Manifest

Assuming the Certificate is already setup in the keyvault use an ExternalSecret to start syncing the certificate.  

externalsecretcert.yml
```YAML
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: <external_secret_name>  # Name of the externalsecret
spec:
  refreshInterval: 10m # How often to resync with the key vault
  secretStoreRef:
    kind: SecretStore
    name: <vault_name> # Name of the secretstore we are referencing

  target:
    name: <name_of_cert_secret_in_k8s> # Name of the cert secret to make in K8S  
    creationPolicy: Owner

    template:
      type: kubernetes.io/tls
      engineVersion: v2

      metadata:
        annotations:
          reflector.v1.k8s.emberstack.com/reflection-allowed: "true"  # Set to true if this cert should appear in all namespaces
          reflector.v1.k8s.emberstack.com/reflection-auto-enabled: "true" # Set to true if this cert should appear in all namespaces

      data:
        tls.crt: '{{ .cert | filterPEM "CERTIFICATE"}}'
        tls.key: '{{ .cert | filterPEM "PRIVATE KEY" }}'


  data:
  - secretKey: cert
    remoteRef:
      key: secret/<cert_name_in_vault> # Name of the cert in the key vault.
```

```
kubectl apply -f externalsecretcert.yml
```

### Create or Update the Keyvault Cert with Azure CLI

AzureCLI must be installed and the cert pem files must be accessible.  

Installing AzureCLI: https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-linux?pivots=apt#installation-options

AzureCLI must be logged into the environment. 
https://docs.microsoft.com/en-us/cli/azure/authenticate-azure-cli

The command to create a new cert or to update an existing is identical.  

Azure requires a PEM cert with both the Cert and Private Key.  This can be created with the cat command.  Assuming the file with Certs / Intermediates is named cabundle.pem and the priv key is named key.pem.  

```bash
cat cabundle.pem key.pem > combined.pem
```

```bash
az keyvault certificate import --vault <vault_name> -n <cert_name_in_vault> -f combined.pem
```