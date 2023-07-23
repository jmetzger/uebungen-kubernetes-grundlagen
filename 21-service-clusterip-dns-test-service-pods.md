# Wir verbinden uns über die ClusterIP und den DNS-Eintrag mit den Pods des Services 

## Schritt 1: ClusterIP herausfinden 

```
kubectl get svc app1 
# z.B.
# 10.245.79.39
``` 

## Schritt 1: busybox starten 

```
kubectl run -it --rm --image=busybox podtester 
```


## Schritt 2: Verbinden über ClusterIP (innerhalb des podtester - pods) 

```
# Innerhalb der busybox eingeben und ausführen
# Macht das ruhig 3x
# Wir wollen nachher sehen, auf welchen Pods er landet 
wget -O - http://<ClusterIP> # aus Schritt 1:
# z.B.
# wget -O - http://10.245.79.39 
```

```
# Ping funktioniert nicht und das ist o.k., ist nicht in der Firewall eingerichtet
ping <ClusterIP>
# z.B.
# ping 10.245.79.39 
```

## Schritt 3: Verbinden über DNS-Namen der ClusterIP (innerhalb des podtester - pods) 

```
# Variante 1: Name des Services
wget -O - http://app1
# Variante 2: Name des Services mit Namespace
wget -O - http://app1.default
# Variante 3: Name des Services mit FQDN (Full Qualified Domain Name)
wget -O - http://app1.default.svc.cluster.local
```

## Schritt 4: busybox beenden 

```
# in busybox
exit
```


## Schritt 5: Dienst löschen und neu erstellen und testen 

```
kubectl delete -f service.yml 
# Service ist nicht mehr da
kubectl get svc 
kubectl apply -f service.yml
# Service ist wieder neu da, aber neue IP 
kubectl get svc app1
```

```
# busybox starten
kubectl run -it --rm --image busybox
```

```
# In der busybox
# DNS funktioniert obwohl neue IP
wget -O - http://app1
# und auch neue ClusterIP z.B .. .. geht
wget -O - http://<ClusterIP>
# z.B.
# wget - http://10.245.116.200
```

```
# busybox beeenden
```

## Schritt 6: Logs aller Pods anschauen 

```
# Wohin wurde connected ?
kubectl logs --prefix -l app=nginx
# Und nur die Logeinträge wo GET drinnen steht ausgeben
# -i = case insensitive (egal ob grosser oder klein geschrieben) 
kubectl logs --prefix -l app=nginx | grep -i get 
```
