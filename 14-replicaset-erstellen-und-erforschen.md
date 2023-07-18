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
kubectl delete pods nginx-replica-set-<nr>; kubectl get pods -w 
# CTRL + c
```

## Schritt 4: Pods überprüfen

```
# Schaut Euch an, wie lange der neue Pod schon läuft
# und auf welchen Nodes die Pods laufen 
kubectl get pods -o wide
```

## Schritt 5: Welche Image-Version wird verwendet 

```
kubectl get pods -o yaml | less
# raus mit q 
```

## Schritt 6: Version mit Nano ändern

```
# nano rs.yml
# Zeile:
# image: nginx:1.25
# durch
image: nginx:1.24
# ersetzen
```

### Schritt 7: Anwenden 

```
kubectl apply -f rs.yml
```

### Schritt 8: Hat sich das image geändern 

```
# keine Änderung der Zeit als auch kein neues Image
kubectl get pods

# Gegenprobe
kubectl describe pods nginx-replica-set<nr>
```

