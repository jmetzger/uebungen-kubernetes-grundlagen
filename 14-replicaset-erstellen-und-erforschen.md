# 14 Replicaset erstellen und erforschen 

## Schritt 1: Erstellen 

```
cd
mkdir -p manifests
cd manifests
mkdir 02-rs 
cd 02-rs 
nano rs.yml
```

```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-replica-set
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      name: template-nginx-replica-set
      labels:
        tier: frontend
    spec:
      containers:
        - name: nginx
          image: nginx:1.25
          ports:
            - containerPort: 80             
```

```
kubectl apply -f rs.yml 
```

## Schritt 2: Erforschen

```
kubectl get all 
```

## Schritt 3: Pod löschen, was passiert ?

```
# Watch the pods 
kubectl delete pods nginx-replicaset-<nr>; kubectl get pods -w 
# CTRL + c
```
