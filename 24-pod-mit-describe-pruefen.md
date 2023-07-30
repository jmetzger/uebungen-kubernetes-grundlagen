# Pod mit describe prüfen 

## Voraussetzung

  * Das Deployment sollte aus einer der letzten Übungen eingerichtet sein
  * Das Projektverzeichnis manifests/03-deploy vorhanden sein.
  * Die Pods aus dem Deployment sollten laufen 
    * kubectl get pods -l apps=nginx 
  * Falls nicht: Bitte nochmal zur einer der vorigen Lektionen zurückgehen und das Deployment einrichten !


## Schritt 1: Einen Pod raussuchen 

```
kubectl get pods -l app=nginx  
# z.B. nginx-deployment-79b55879bb-5rkbz
```

## Schritt 2: Den Pod prüfen 

```
# z.B. Hier Deinen eigenen Pod einsetzen
kubectl get pods nginx-deployment-79b55879bb-5rkbz
kubectl describe pods nginx-deployment-79b55879bb-5rkbz
```

## Schritt 3: Einen pod starten, der nicht laufen kann (weil es das image nicht gibt

```
kubectl run --image=foo foomaster
# Hier sieht es aus, als würde er laufen
kubectl get pods foomaster 
# Das zeigt uns die Wahrheit !!!
kubectl describe foomaser
```

## Schritt 4: Den Pod löschen 

```
kubectl delete po foomaster
```
