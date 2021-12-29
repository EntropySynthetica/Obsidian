Deployment

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: demo-deploy
  name: demo-deploy
  namespace: demo
  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demodeploy
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: demo-deploy
    spec:
      containers:
      - image: nginx:1.21.3
        imagePullPolicy: Always
        name: demo-deploy
        resources: {}
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: github-registry
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30

```

Configmap
```YAML
apiVersion: v1
kind: ConfigMap
metadata:
  name: demo-configmap
  namespace: demo

data:
  KEY1: value1
  KEY2: value2
  KEY3: value3
 
```

ClusterIP Service
```YAML
apiVersion: v1
kind: Service # Specify the type of manifest this is.  In this case a service, which is used to allow network access to pods.

metadata:
  labels:
    app: demo-service
  name: demo-service
  namespace: demo # Specify the name of the namespace to run this object under.

spec:
  type: ClusterIP # Type of Service.  Options are ClusterIP or Node Port
  clusterIP: None  # Specify an IP for the service.  None means K8S will auto assign an IP.

  selector:
    app: demo-deploy # Traffic to this service will be routed to pod(s) with this label.

  sessionAffinity: None

  ports:
    - name: default
      port: 80 # Port the service will be listening on
      protocol: TCP
      targetPort: 80 # Port that pod(s) will be listening on
```

Node Port Service (Optional)
```YAML
apiVersion: v1
kind: Service
metadata:
  labels:
    app: demo-service-nodeport
  name: demo-service-nodeport
  namespace: actionsdemo

spec:
  externalTrafficPolicy: Cluster
  ports:
  - nodePort: 32180
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: demo-deploy
  sessionAffinity: None
  type: NodePort
```

Ingress

```YAML
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: demo-ingress
  namespace: demo
  
spec:
  rules:
  - host: demo.something.com
    http:
      paths:
      - backend:
          serviceName: demo-service
          servicePort: 80
		  
  tls:
  - hosts:
    - demo.something.com
    secretName: certbot-demo
```