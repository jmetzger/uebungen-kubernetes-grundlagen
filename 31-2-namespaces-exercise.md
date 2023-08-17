# Praxisübung namespaces 

## Schritt 1: namespaces anzeigen 

```
kubectl get ns
```

## Schritt 2: 2 namespaces erstellen 

```
kubectl create ns monitoring-app
kubectl create ns booking-app
```

## Schritt 3: manifests in einem bestimmten namespace ausrollen 

```
cd manifests
mkdir 31-namespaces
cd 31-namespaces
nano 01-pod.yaml
```

```
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
kubectl -n monitoring-app -f 01-pod.yml
# all in einem bestimmten namespaces anzeigen
kubectl -n monitoring-app all 
```

## Schritt 4: namespaces wechseln (default und parameter) 

```
# client so einstellen, dass er immer einen bestimmten namespaces standardmäßig nimmt
kubectl config set-context --current --namespace=monitoring-app
```

```
# anderen namespace abfragen
kubectl -n booking-app get all
```

```
# zurücksetzen auf default
kubectl config set-context --current --namespace=default
```

## Schritt 5: namespace löschen 

```
kubectl delete ns booking-app
kubectl delete ns monitoring-app
```


