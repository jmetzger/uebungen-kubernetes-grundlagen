# Nginx mit einer statischen Seite erstellen (configmap)

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
mv services.yml app1
mkdir app2
cd app2
```

