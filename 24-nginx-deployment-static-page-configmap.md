# Nginx-Deployment mit einer statischen Seite erstellen (configmap)

## Voraussetzungen 

  * Ordner manifests/03-deploy ist vorhanden mit deployment.yml und service.yml 
  * Wenn nicht gehe bitte zu den vorigen Lektionen zurück.

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
kubectl apply -f .
```

## Schritt 3: Configmap einbinden 

```
# cm = configmap 
nano 02-deployment-cm.yml
```

```

```
