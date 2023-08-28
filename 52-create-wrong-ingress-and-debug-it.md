# Wir erstellen einen Ingress-Config und finden den Fehler ! 

## Voraussetzungen 

  * Es existieren bereits die Services:
  * Es existieren die Deployments
  * Wenn nicht, gehe zu den vorigen Übungen zurück und erstelle sie. 

## Schritt 1: Die falsche Ingress-Config verwenden 

  * Wir verwenden eine falsche Ingress-Config aus dem Internet und Debuggen sie.

```
# Ingress
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: "app1.dein-training.de
    http:
      paths:
        - path: /
          backend:
            serviceName: apple-service
            servicePort: 80
  - host: "app1.dein-training.de"
    http:
      paths:
        - path: /
          backend:
            serviceName: apple-service
            servicePort: 80


```

```
# ingress - Beim Applying tritt ein Problem auf 
kubectl apply -f ingress.yml
```

## Schritt 2: Wir finden/lösen das Problem 

### (Mini-)Schritt 2.1 

  * Den richtige api-resource and die richtige appVersion finden 

```



````


```
# Hints 

# 1. Which resources does our version of kubectl support 
# Can we find Ingress as "Kind" here.
kubectl api-ressources 

# 2. Let's see, how the configuration works 
kubectl explain --api-version=networking.k8s.io/v1 ingress.spec.rules.http.paths.backend.service

# now we can adjust our config 
```

## Solution

```
# in kubernetes 1.22.2 - ingress.yml needs to be modified like so.
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    ingress.kubernetes.io/rewrite-target: /
    # with the ingress controller from helm, you need to set an annotation 
    # old version useClassName instead 
    # otherwice it does not know, which controller to use
    # kubernetes.io/ingress.class: nginx 
spec:
  ingressClassName: nginx
  rules:
  - host: "app12.lab.t3isp.de"
    http:
      paths:
        - path: /apple
          pathType: Prefix
          backend:
            service:
              name: apple-service
              port:
                number: 80
        - path: /banana
          pathType: Prefix
          backend:
            service:
              name: banana-service
              port:
                number: 80                
```
