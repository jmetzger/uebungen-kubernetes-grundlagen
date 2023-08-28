# Wir erstellen den 2. Service (für das statische Deployment - nginx-custom-index) 

## Voraussetzungen 

  * Deployment aus der vorigen Übung besteht.
    * kubectl get deploy nginx-custom-index
  * Wenn nicht, gehe bitte nochmal zur vorigen Übung zurück und erstelle dieses Deployment 
   
## Schritt 1: Service - manifest erstellen 

```
cd 
mkdir -p manifests/03-deploy/app2 
cd manifests/03-deploy/app2
nano service.yml 
```

```
# nano service.yml
apiVersion: v1
kind: Service
metadata:
  name: app2
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: nginx-app2 
```

```
kubectl apply -f .
```

### Schritt 2: Überprufen

```
kubectl get svc app2
# Endpunkte gefunden ?
kubectl describe svc app2
# Alle anzeigen, wenn es zuviele sind
kubectl get endpoints app2 
```
