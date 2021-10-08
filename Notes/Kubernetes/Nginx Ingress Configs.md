---
tags: [Kubernetes]
title: Nginx Ingress Configs
created: '2020-07-26T22:23:13.452Z'
modified: '2020-07-26T22:25:03.403Z'
---
# Nginx Ingress Configs

## Nginx Ingress Basic Auth

Generate a password file

```bash
htpasswd -c auth <username>
```

Use Kubectl to create a secret from the file

```bash
kubectl create secret generic basic-auth --from-file=auth
```

Verify the secret

```bash
kubectl get secret basic-auth -o yaml
```

Add the following annotations to the ingress rule like the example below

```yaml
apiVersion: networking.k8s.io/v1
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
			service:
				name: http-svc
				port:
					number: 80
```


## Nginx Multipath example

```YAML
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-ingress1
  namespace: demo
  annotations:
    kubernetes.io/ingress.class: "nginx"

spec:
  rules:
    - host: demo.local 
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: deploy1-service 
                port:
                  number: 80  

---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-ingress2
  namespace: demo 
  annotations:
    kubernetes.io/ingress.class: "nginx"
    nginx.ingress.kubernetes.io/rewrite-target: /$2  # This ensures the /redis in the URI doesn't make it to the backend.
    nginx.ingress.kubernetes.io/configuration-snippet: |
      rewrite ^(/redis)$ $1/ redirect;  # This adds /redis into relative links in the HTML body.  

spec:
  rules:
    - host: demo.local 
      http:
        paths:
          - path: /redis(/|$)(.*)
            pathType: Prefix
            backend:
              service:
                name: rediscommander-service
                port:
                  number: 80

---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-ingress3
  namespace: demo
  annotations:
    kubernetes.io/ingress.class: "nginx"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/proxy-redirect-from: "http://demo.local/"
    nginx.ingress.kubernetes.io/proxy-redirect-to: "http://demo.local/gateway/"
    nginx.ingress.kubernetes.io/configuration-snippet: |
      proxy_set_header Accept-Encoding "";  # This turns off http compression. This is needed for sub_filter to work
      rewrite ^(/gateway)$ $1/ redirect;  # This will add /gateway into absolute links. 
      sub_filter_once off;
      sub_filter_types *;
      sub_filter "http://demo.local" "http://demo.local/gateway";  # This adds the /gateway into the absolute links. 


spec:
  rules:
    - host: demo.local
      http:
        paths:
          - path: /gateway(/|$)(.*)
            pathType: Prefix
            backend:
              service:
                name: demo-gateway-service
                port:
                  number: 80
```