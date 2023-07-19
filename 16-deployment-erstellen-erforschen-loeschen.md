# Ein Deployment erstellen

## Erstellen
```
cd
cd manifests
mkdir 03-deploy
cd 03-deploy 
nano deploy.yml 
```

```
# nano deploy.yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 8 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.24
        ports:
        - containerPort: 80
        
```

```
kubectl apply -f deploy.yml 
```

## Erforschen 

### Wo seid ihr ? 

```
# Alles anzeigen / betrachten
kubectl get all
```

```
# ein pod löschen
kubectl delete po nginx-deployment-<hash>-<nr>
# Wird ein neuer erstellt ?
kubectl get pods -w
```

### Die Image Version ändern 

```
# nano deploy.yml
# Zeile: image: nginx:1.24
# ändern nach:
image: nginx:1.25
```

```
# Wir wollen schnell alles sehen  ;o) 
kubectl apply -f deploy.yml; kubectl get all; kubectl get pods -w 
```

## Löschen 

```
kubectl delete -f deploy.yml
kubectl get all
```
