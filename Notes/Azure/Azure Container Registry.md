# Azure Container Registry

## Remove Untagged Images

### Manual Run

Run the command with --dry-run to verify what it will remove.  

The --ago flag specifies how old to keep tagged images for.  Set this really high to keep old tagged images.  

```bash
az acr run \
  --cmd "acr purge --ago 360d --filter 'image-name:.*' --untagged --dry-run" \
  --registry registry-name \
  /dev/null
```

Then run without the --dry-run to clear out the repository
```bash
az acr run \
  --cmd "acr purge --ago 360d --filter 'image-name:.*' --untagged" \
  --registry registry-name \
  /dev/null
```

### Schedule as a reoccurring task

```bash
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd "acr purge --ago 360d --filter 'image-name:.*' --untagged" \
  --schedule "0 21 * * *" \
  --context /dev/null
```