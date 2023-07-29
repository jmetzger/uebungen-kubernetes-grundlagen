# Nginx-Deployment mit einer statischen Seite erstellen (configmap)

## Voraussetzungen 

  * Ordner manifests/03-deploy ist vorhanden mit deployment.yml und service.yml 
  * Wenn nicht gehe bitte zu den vorigen Lektionen zurück und erstelle den Service und das Deployment (app1)

## Schritt 1: Umbau  

```
# Wir verschönern die Struktur 
cd
cd manifests/03-deploy
mkdir app1
mv deploy.yml app1
mv service.yml app1
mkdir app2
cd app2
```

## Schritt 2: Configmap erstellen 

```
nano 01-configmap.yml
```

```
# in 01-configmap.yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-configmap-index
data:
  index.html: |
    <html>
    <title> Jedi - Challenge </title>
    <body>
    </br>
    <h1>Willkommen, junger Padawan !</h1>
    </body>
    </html>
```

```
# Denke an den Punkt am Ende ;o)
kubectl apply -f .
```

## Schritt 3: Configmap erforschen

```
kubectl get cm
kubectl get cm nginx-configmap-index
# genauere Infos bekommen 
kubectl describe cm nginx-configmap-index
# Alternative
kubectl get cm nginx-configmap-index -o yaml
```

## Schritt 4: Configmap einbinden 

```
# cm = configmap 
nano 02-deployment-cm.yml
```

```
# nano deploy.yml 
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-custom-index
spec:
  selector:
    matchLabels:
      app: nginx-app2
  replicas: 8 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx-app2
    spec:
      containers:
      - name: nginx
        image: nginx:1.24
        ports:
        - containerPort: 80
        
```

```
kubectl apply -f 02-deployment-cm.yml 
```

## Schritt 5: Testen/Erforschen (Deployment vorhanden ;o) ?)

```
# Ist das neue deployment da
kubectl get all 
# wir lassen uns nur alle Objekte mit nginx-app2 anzeigen
kubectl get all -l app=nginx-app2 
```

## Schritt 6: Zugriff testen (eines Pods aus neuem Deployment)

```
# PodIP eines beliebigen Pods raussuchen
# z.B. 
kubectl get pods -o wide -l app=nginx-app2
```

```
# busybox starten 
kubectl run -it --rm --image=busybox podtester
````

```
# Im busybox - pod (container)
# Pod anpingen
# ping <PodIP>
# z.B.
ping  -c 4 10.244.1.99

# Nginx aufrufen
wget -O - http://<PodIP>
# z.B.
wget -O - http://10.244.1.99
```

```
# busybox pod beenden und verlassen
exit
```
