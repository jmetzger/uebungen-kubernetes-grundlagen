# Beispiel: Statischer Nginx Pod

## Walkthrough 

```
cd 
mkdir -p manifests
cd manifests
mkdir -p web
cd web
```

```
# nano nginx-static.yml 

apiVersion: v1
kind: Pod
metadata:
  name: nginx-static-web
  labels:
    webserver: nginx
spec:
  containers:
  - name: web
    image: nginx:1.25.1

```

```
kubectl apply -f nginx-static.yml 
kubectl describe pod nginx-static-web 
# Zeige die Konfiguration
kubectl get pod/nginx-static-web -o yaml
kubectl get pod/nginx-static-web -o wide 
```
