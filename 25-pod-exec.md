# In den Pod mit exec hinweinwechseln 

## Voraussetzung

  * Das Deployment sollte aus einer der letzten Übungen eingerichtet sein
  * Das Projektverzeichnis manifests/03-deploy vorhanden sein.
  * Die Pods aus dem Deployment sollten laufen 
    * kubectl get pods -l app=nginx 
  * Falls nicht: Bitte nochmal zur einer der vorigen Lektionen zurückgehen und das Deployment einrichten !

## Schritt 1: Einen Pod raussuchen 

```
kubectl get pods -l app=nginx  
# z.B. nginx-deployment-79b55879bb-5rkbz
```

## Schritt 2: In den pod hineinwechseln

```
# -> Hier Deinen eigenen Pod-Namen einsetzen <- 
kubectl exec -it nginx-deployment-79b55879bb-5rkbz -- bash 
# oder
kubectl exec -it nginx-deployment-79b55879bb-5rkbz -- bash
```

## Schritt 3: Analysieren / Kommandos im Pod ausführen 

```
# Welches Linux - geht oft aber nicht immer: 
cat /etc/os-release
ls -la
cat /etc/passwd
# Welcher Nutzer bin ich
id
```

```
# Folgende Kommandos gehen oftmals nicht,
# weil sie nicht im Image installiert sind
ping www.google.de
```

```
# pod wieder verlassen
exit
```

## Schritt 4: Ein Kommando in einem Pod ausführen

```
kubectl exec nginx-deployment-79b55879bb-5rkbz -- ls -la

```
