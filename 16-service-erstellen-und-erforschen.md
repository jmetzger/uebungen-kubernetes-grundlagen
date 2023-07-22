# Wir erstellen den 1. Service und erforschen ihn

## Voraussetzung

 * Die Dateien aus dem vorigen Deployment sollte noch da sein.
 * Wenn nicht, gehe bitte nochmal zurück und erstelle die Dateien

## Der Weg zum Service 

### Schritt 1: Deployment nochmal ausführen 

```
cd manifests
cd 03-deploy
kubectl apply -f deploy.yml
```

```
# Objekte in den manifesten anzeigen
kubectl get all.
```

### Schritt 2: Wir bauen einen Service dazu 

```
# nano service.yml
apiVersion: v1
kind: Service
metadata:
  name: app1
  labels:
    svc: nginx
  spec:
    ports:
    - port: 80
      protocol: TCP
    selector:
      app: nginx 
```

```
kubectl apply -f .
```

### Schritt 3: Erforschen 

#### Endpunkte gefunden ? 

```
kubectl describe svc app1
# er even better, too see all endpoints if there are too many ;o)
kubectl describe endpoints app1 
```

#### Ein Pod löschen 

```
# Alle Pods mit PodIPs anzeigen
kubectl get po -o wide

# Den 1. Pod löschen -> Merke Dir die PodIP
# !! Notiere Dir die <hash> und die <nr> aus der letzten Ausgabe !!
kubectl delete po nginx-deployment-<hash>-<nr>
# e.g. kubectl delete po nginx-deployment-nginx-deployment-79b55879bb-84r76 # ip: 10.244.0.246
```

#### Skaliere runter und wieder hoch ;o) 

```
## Skaliere runter 
# nano deploy.yml
# Ändere die Replicas von 8 auf 2
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
```

```
kubectl apply -f .
```

```
kubectl get all
# Haben wir jetzt nur noch 2 Endpunkte (PodIPs) ?
kubectl describe svc app1
kubectl describe endpoints app1
```

```
## Und skaliere wieder hoch 
# nano deploy.yml
# Ändere die Replicas von 2 zurück auf 8
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 8
```

```
kubectl apply -f
```

```
kubectl get all
# Haben wir jetzt wieder 8 Endpunkte (PodIPs) 
kubectl describe svc app1
kubectl describe endpoints app1
```
