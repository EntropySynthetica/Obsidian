---
tags: [Kubernetes]
title: Hasicorp Vault Notes
created: '2020-08-31T01:45:19.253Z'
modified: '2020-10-01T13:49:37.573Z'
---

# Hasicorp Vault Notes

## Install the Vault Binary to manage a vault server.  


Add the Hashicorp GPG key
```bash
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
```

Add the official Hashicorp Linux Repo
```bash
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
```

Update and Install Vault
```bash
sudo apt-get update && sudo apt-get install vault
```

Verify it is installed 
```bash
vault
```

---

After starting the vault server check the startup logs,  the root token will be in there. 

## Connecting the Vault Binary

Setup your exports,
```bash
export VAULT_ADDR='http://127.0.0.1:8200'
```

```bash
export VAULT_TOKEN="s.XmpNPoi9sRhYtdKHaQhkHP6x"
```

Verify it is connecting
```bash
vault status
```


---
## Setup SSH Signed Certificates

Mount the secrets engine.
```bash
vault secrets enable -path=ssh-client-signer ssh
```

Create a key pair
```bash
vault write ssh-client-signer/config/ca generate_signing_key=true
```

Add the key to the hosts SSH config

```bash
curl -o /etc/ssh/trusted-user-ca-keys.pem http://<vault IP>:8200/v1/ssh-client-signer/public_key
```

Add the following line to the end of `/etc/ssh/sshd_config`

```
TrustedUserCAKeys /etc/ssh/trusted-user-ca-keys.pem
```

Restart the SSH service on the host. 


Create a Vault role for signing keys

```bash
vault write ssh-client-signer/roles/my-role -<<"EOH"
{
  "allow_user_certificates": true,
  "allowed_users": "*",
  "allowed_extensions": "permit-pty,permit-port-forwarding",
  "default_extensions": [
    {
      "permit-pty": ""
    }
  ],
  "key_type": "ca",
  "default_user": "sysadmin",
  "ttl": "5m0s"
}
EOH
```

On your client ask vault to generate and save a signed key you can use to log into the host. 

```bash
vault write -field=signed_key ssh-client-signer/sign/my-role public_key=@$HOME/.ssh/id_rsa.pub > ~/.ssh/id_rsa-cert.pub
```

You can now SSH into the host until the TTL expires. 

You can verify the signed cert with the following command

```bash
ssh-keygen -Lf ~/.ssh/id_rsa-cert.pub
```

