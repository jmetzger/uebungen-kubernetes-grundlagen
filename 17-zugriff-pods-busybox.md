# Deployment - Wie können wir auf die Pods zugreifen ?

## Schritt 1: Pod-IP rausfinden 

```
# raussuchen und eine pod-ip notieren
# z.B.  10.244.0.246
kubectl get pods -o wide
```

## Schritt 2: Pod für kurzfristige Arbeiten starten

```
kubectl run podtest --rm -it --image busybox 
```

## Schritt 3: Maßnahmen im Pod durchführen 

```
# ping funktioniert -> ACHTUNG: hier eigene IP aus Schritt 1 nehmen 
ping -c 4 10.244.0.246 
# Seite kann geladen werden -> ACHTUNG: hier eigene IP as Schritt 1 nehmen 
wget -O - http://10.244.0.246
# und pod beenden
exit
```
```
# HINWEIS: Der Pod wird nach dem exit automatisch beendet und gelöscht ;o)
```
