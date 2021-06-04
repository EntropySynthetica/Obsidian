---
tags: [Kubernetes]
title: Nginx Ingress Basic Auth
created: '2020-07-26T22:23:13.452Z'
modified: '2020-07-26T22:25:03.403Z'
---

# Nginx Ingress Basic Auth

Generate a password file

`htpasswd -c auth <username>`

Use Kubectl to create a secret from the file

`kubectl create secret generic basic-auth --from-file=auth`

Verify the secret

`kubectl get secret basic-auth -o yaml`

Add the following annotations to the ingress rule like the example below

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: ingress-with-auth
  annotations:
    # type of authentication
    nginx.ingress.kubernetes.io/auth-type: basic
    # name of the secret that contains the user/password definitions
    nginx.ingress.kubernetes.io/auth-secret: basic-auth
    # message to display with an appropriate context why the authentication is required
    nginx.ingress.kubernetes.io/auth-realm: 'Authentication Required - foo'
spec:
  rules:
  - host: foo.bar.com
    http:
      paths:
      - path: /
        backend:
          serviceName: http-svc
          servicePort: 80
```
