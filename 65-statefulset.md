# Wir erstellen ein Statefulset 

## Schritt 1: Erstellen

```
cd
mkdir -p manifests/04-sts/
cd manifests/04-sts
nano statefulset.yml
```

```
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx-sts
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
  replicas: 2
  selector:
    matchLabels:
      app: nginx-sts
  template:
    metadata:
      labels:
        app: nginx-sts
    spec:
      containers:
      - name: nginx
        image: registry.k8s.io/nginx-slim:0.8
        ports:
        - containerPort: 80
          name: web
```

```
kubectl apply -f .
```

## Schritt 2: Erkunden 

```
kubectl run -it --rm podtester --image=busybox
```

```
# in der busybox 
ping -c4 web-0.nginx 
ping -c4 web-1.nginx 
exit
```

## Referenz 

  * https://kubernetes.io/docs/tutorials/stateful-application/basic-stateful-set/

