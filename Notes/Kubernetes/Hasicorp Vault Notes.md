---
tags: [Kubernetes]
title: Hasicorp Vault Notes
created: '2020-08-31T01:45:19.253Z'
modified: '2020-10-01T13:49:37.573Z'
---

# Hasicorp Vault Notes

## Install the Vault Binary to manage a vault server.  


Add the Hashicorp GPG key
```
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
```

Add the offical Hashicorp Linux Repo
```
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
```

Update and Install Vault
```
sudo apt-get update && sudo apt-get install vault
```

Verify it is installed 
```
vault
```

---

After starting the vault server check the startup logs,  the root token will be in there. 

## Connecting the Vault Binary

Setup your exports,
```
export VAULT_ADDR='http://127.0.0.1:8200'
```

```
export VAULT_TOKEN="s.XmpNPoi9sRhYtdKHaQhkHP6x"
```

Verify it is connecting
```
vault status
```


---
## Setup SSH Signed Certificates

Mount the secrets engine.
```
vault secrets enable -path=ssh-client-signer ssh
```

Create a key pair
```
vault write ssh-client-signer/config/ca generate_signing_key=true
```

Add the key to the hosts SSH config

```
curl -o /etc/ssh/trusted-user-ca-keys.pem http://<vault IP>:8200/v1/ssh-client-signer/public_key
```

Add the following line to the end of `/etc/ssh/sshd_config`

```
TrustedUserCAKeys /etc/ssh/trusted-user-ca-keys.pem
```

Restart the SSH service on the host. 


Create a Vault role for signing keys

```
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

```
vault write -field=signed_key ssh-client-signer/sign/my-role public_key=@$HOME/.ssh/id_rsa.pub > ~/.ssh/id_rsa-cert.pub
```

You can now SSH into the host until the TTL expires. 

You can verify the signed cert with the following command

```
ssh-keygen -Lf ~/.ssh/id_rsa-cert.pub
```

