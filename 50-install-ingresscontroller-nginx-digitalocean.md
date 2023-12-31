# Wir installieren den Ingress Controller (nginx) in unserem Cluster (DigitalOcean DOKS)

## Grundlagen

  * Das Verfahren funktioniert auch so auf anderen Plattformen, wenn helm verwendet wird und noch kein IngressController vorhanden
  * Ist kein IngressController vorhanden, werden die Ingress-Objekte zwar angelegt, es funktioniert aber nicht. 

## Voraussetzungen 

  * helm und kubectl müssen installiert sein und Du musst auf das Cluster zugreifen können

## Walkthrough (Installation Ingress Controller - nginx) 

```
# Repo hinzufügen
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
# Den letzten Stand der Charts holen 
helm repo update

# --namespace :: Bitte im Namespace ingress installieren
# --create-namespace :: Bitte Namespace erstellen, wenn nicht vorhanden
helm install nginx-ingress ingress-nginx/ingress-nginx --namespace ingress --create-namespace

# Es dauert jetzt ein wenig, bis wir eine External-IP bekommen
kubectl -n ingress get all
kubectl --namespace ingress get services -o wide -w nginx-ingress-ingress-nginx-controller
```

```
# Beispielausgabe  
NAME                                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE     SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.245.78.34   157.245.20.222   80:31588/TCP,443:30704/TCP   4m39s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

## Referenz

  * [https://kubernetes.github.io/ingress-nginx/deploy/](https://artifacthub.io/packages/helm/ingress-nginx/ingress-nginx)https://artifacthub.io/packages/helm/ingress-nginx/ingress-nginx
