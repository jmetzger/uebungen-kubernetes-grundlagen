# Zeige mir die Logs des / der Pods 

## Voraussetzung

  * Das Deployment sollte aus einer der letzten Übungen eingerichtet sein
  * Der Service sollte mit type: LoadBalancer eingerichtet sein 
  * Das Projektverzeichnis manifests/03-deploy vorhanden sein.
  * Die Pods aus dem Deployment sollten laufen 
    * kubectl get pods -l app=nginx 
  * Falls nicht: Bitte nochmal zur einer der vorigen Lektionen zurückgehen und das Deployment einrichten !

## Schritt 1: Einen Pod raussuchen 

```
kubectl get pods -l app=nginx  
# z.B. nginx-deployment-79b55879bb-5rkbz
```

## Schritt 2: Die Logs dieses einen Pods anzeigen 

```
# -> Hier Deinen eigenen Pod-Namen einsetzen <- 
kubectl logs nginx-deployment-79b55879bb-5rkbz  

# Wenn es sehr viele sind, kannst du auch einfach ein "less" dahinterhängen
kubectl logs nginx-deployment-79b55879bb-5rkbz | less

# oder aber nur die der letzten Stunde anschauen
kubectl logs --since=1h nginx-deployment-79b55879bb-5rkbz
```

## Schritt 3: Die Logs aller Pods mit einem bestimmten Label ausgeben 

```
kubectl logs -l app=nginx --prefix
# hier könntest du auch bereits nach bestimmten Suchworten selektieren
kubectl logs -l app=nginx --prefix | grep -i error 
```

## Schritt 4: Die Logs aller Pods mit einem bestimmten Label FORTLAUFEND ausgeben

```
# EXTERNAL-IP des LoadBalancers für den Service rausfinden
# z.B. 188.166.194.18
kubectl get svc app1 
```

```
# prefix zeigt zusätzlich die Quelle (d.h. den Pod an)
# Sehr zu empfehlen 
kubectl logs -l app=nginx --max-log-requests=8 --prefix -f
```

```
# Öffne einen Browser mit Deiner EXTERNAL-IP
# http://188.166.194.18
```

```
# Im Terminal Ausgabe mit
STRG + C
# beenden
```

```
# bestimmte Suchworte selektieren
kubectl logs -l app=nginx --prefix | grep -i error 
```

```
# Öffne einen Browser mit Deiner EXTERNAL-IP
# http://188.166.194.18
```

```
# Im Terminal Ausgabe mit
STRG + C
# beenden
```
