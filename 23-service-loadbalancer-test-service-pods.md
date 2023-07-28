# Den Service von aussen erreichen (LoadBalancer = EXTERNAL-IP) 

## Voraussetzung

  * Der Service sollte aus der letzten Übung noch eingerichtet sein
  * Die Datei service.yml sollte im Verzeichnis manifests/03-deploy vorhanden sein.
  * Falls nicht: Bitte nochmal zur Lektion zum Einrichten des Deployments zurückgehen!

## Schritt 1: Service anpassen und apply'en

```
# nano service.yml 
# Datei service.yml anpassen
# type: NodePort (Bitte Groß- und Kleinschreibung beachten)
# muss ergänzt werden
spec:
  type: LoadBalancer
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: nginx
```

```
kubectl apply -f service.yml
```

## Warten auf die externe IP (kann mehrere Minuten dauern) 

```
# Warten auf Eintrag/Vergabe der EXTERNAL-IP
kubectl get svc app1 -w 
```

## Schritt 2: Daten herausfinden.

```
# Externe IP = EXTERNAL-IP herausfinden
# Port ist zwar auch da, wird aber aktuell nicht benötigt
kubectl get svc app1 
```

## Schritt 3: Von Extern aufrufen 

```
# Über wget
# Daten aus Schritt 1
wget -O - http://<EXTERNAL-IP>
# z.B.
wget -O - http://62.51.34.12
```

```
# oder den Browser
# Im browser eingeben
http://<EXTERNAL-IP>
# z.B.
# http://62.51.34.12
```


