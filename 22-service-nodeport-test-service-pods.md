# Den Service von aussen erreichen (NodePort) 

## Voraussetzung

  * Der Service sollte aus der letzten Übung noch eingerichtet sein
  * Die Datei service.yml sollte im Verzeichnis manifests/03-deploy vorhanden
  * Wenn nicht: !Bitte nochmal zur vorigen Lektion zurückgehen!


## Schritt 1: Service anpassen und apply'en

```
# nano service.yml 
# Datei service.yml anpassen
# type: NodePort (Bitte Groß- und Kleinschreibung beachten)
# muss ergänzt werden
spec:
  type: NodePort
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: nginx
```

```
kubectl apply -f service.yml
```

## Schritt 2: Daten herausfinden.

```
# Externe IP herausfinden
# Wir suchen uns eine aus, z.B.  157.230.27.52
kubectl get nodes -o wide

# Freigeschalteten NodePort herausfinden
kubectl get svc app1
# z.B. 31337 
```

## Schritt 3: Von Extern aufrufen 

```
# Über wget
# Daten aus Schritt 1
wget -O - http://<deine-externe-ip>:<dein-node-port>
# z.B.
wget -O - http://157.230.27.52:31337
```

```
# oder den Browser
# Im browser eingeben
http://<deine-externe-ip>:<dein-node-port>
# z.B.
# http://157.230.27.52:31337
```
```
