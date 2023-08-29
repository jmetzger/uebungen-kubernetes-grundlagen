# Wir erstellen eine Ingress-Config und finden den Fehler ! 

## Voraussetzungen 

  * Es existieren bereits die Services:
  * Es existieren die Deployments
  * Wenn nicht, gehe zu den vorigen Übungen zurück und erstelle sie. 

## Schritt 1: Die falsche Ingress-Config verwenden 

  * Wir verwenden eine falsche Ingress-Config aus dem Internet und Debuggen sie.

```
cd
mkdir -p manifests/03-deploy 
cd manifests/03-deploy
mkdir -p ingresscontroller
cd ingresscontroller
nano ingress.yml
```

```
# Ingress
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: apps-ingress
  annotations:
    ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: app1.dein-training.de
    http:
      paths:
        - path: /
          backend:
            serviceName: app1
            servicePort: 80
  - host: app2.dein-training.de
    http:
      paths:
        - path: /
          backend:
            serviceName: app2
            servicePort: 80


```

```
# ingress - Beim Applying tritt ein Problem auf 
kubectl apply -f .
```

```
error: resource mapping not found for name: "apps-ingress" namespace: "" from "ingress.yml": no matches for kind "Ingress" in version "extensions/v1beta1"
ensure CRDs are installed first
```

## Schritt 2: Wir finden/lösen das Problem 

### (Mini-)Schritt 2.1 api-resource und ApiVersion identifizieren 

  * Den richtige api-resource and die richtige appVersion finden 

```
# Bitte nur die ersten 3 Zeilen anzeigen -> head -n 3  
kubectl explain ingress | head -n 3
```

```
# Nur Anzeige, nicht eingeben 
# GROUP:      networking.k8s.io
# KIND:       Ingress
# VERSION:    v1
```

```
# Wir erkennen das die richtige API-Ressource
# NICHT extensions/v1beta1
# SONDERN networking.k8s.io/v1
```

### (Mini-)Schritt 2.2. api-resource und apiVersion ändern und ausführen 

```
# ist und korrigieren das.
nano ingress.yml 
```

```
# nur 
# apiVersion ändern wie folgt
apiVersion: networking.k8s.io
# ändern 
```

```
# Das gesamte File sieht jetzt so aus:
# Ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: apps-ingress
  annotations:
    ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: app1.dein-training.de
    http:
      paths:
        - path: /
          backend:
            serviceName: app1
            servicePort: 80
  - host: app2.dein-training.de
    http:
      paths:
        - path: /
          backend:
            serviceName: app2
            servicePort: 80
```

``` unknown field "spec.rules[0].http.paths[0].backend.serviceName"
kubectl apply -f .
```


### (Mini-)Schritt 2.3: Fehler verstehen 

```
# Hier der letzte Fehler aus Schritt 2.2.
Error from server (BadRequest): error when creating "ingress.yml": Ingress in version "v1" cannot be handled as a Ingress: strict decoding error: unknown field "spec.rules[0].http.paths[0].backend.serviceName", unknown field "spec.rules[0].http.paths[0].backend.servicePort", unknown field "spec.rules[1].http.paths[0].backend.serviceName", unknown field "spec.rules[1].http.paths[0].backend.servicePort"
```

```
# Auszug
unknown field "spec.rules[0].http.paths[0].backend.serviceName", unknown field "spec.rules[0].http.paths[0].backend.servicePort", unknown field

# Bedeutet
# Ich kenne das Feld spec..backend.servicePort nicht.

# Heisst aber auch:
# Das Feld spec.rules.http.paths.backend .. kenne ich schon
```

```
# Wir forschen
kubectl explain ingress.spec.rules.http.paths.backend
```

```
# Er kennt eine Eigenschaft service, aber eben nich serviceName
FIELD: backend <IngressBackend>
...
  service       <IngressServiceBackend>
    service references a service as a backend. This is a mutually exclusive
    setting with "Resource".
```

```
# Was möchte er unter service haben ? 
kubectl explain ingress.spec.rules.http.paths.backend.service
```

```
# Bingo: name 
GROUP:      networking.k8s.io
KIND:       Ingress
VERSION:    v1

FIELD: service <IngressServiceBackend>
[...]
FIELDS:
  name  <string> -required-
    name is the referenced service. The service must exist in the same namespace
    as the Ingress object.
```

```
# Ergebnis:
# Statt:
# ingress.spec.rules.http.paths.backend.serviceName
# Erwartet Kubernetes jetzt:
# ingress.spec.rules.http.paths.backend.service.name
```
 
### (Mini-)Schritt 2.4: Lösung umsetzen

```
nano ingress.yml
````

```
# Wir passen das manifest wie folgt an:
# Ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: apps-ingress
  annotations:
    ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: app1.dein-training.de
    http:
      paths:
        - path: /
          backend:
            # serviceName: app1 # Vorher
            # Jetzt:
            service:
              name: app1
            servicePort: 80
  - host: app2.dein-training.de
    http:
      paths:
        - path: /
          backend:
            # serviceName: app2 # Vorher
            # Jetzt:
            service:
              name: app2
            servicePort: 80
```

```
kubectl apply -f .
# --> Fehler 
```

### (Mini-)Schritt 2.5: Nächsten Fehler verstehen und umsetzen (servicePort) 

```
# Folgender Fehler nach kubectl apply -f .
Error from server (BadRequest): error when creating "ingress.yml": Ingress in version "v1" cannot be handled as a Ingress: strict decoding error: unknown field "spec.rules[0].http.paths[0].backend.servicePort", unknown field "spec.rules[1].http.paths[0].backend.servicePort"

# <- servicePort kennt er nicht
```

```
# Schrittweise debuggen
kubectl explain ingress.spec.rules.http.paths.backend
kubectl explain ingress.spec.rules.http.paths.backend.service
kubectl explain ingress.spec.rules.http.paths.backend.service.port
# Und er braucht auch nocht Number
kubectl explain ingress.spec.rules.http.paths.backend.service.port.number

# so würde die Eigenschaft dann im yml-file aussehen.
# service:
#   port:
#     number: 80
```

```
# Wir setzen das um
nano service.yml
```

```
# So sieht das korrigierte .yml file aus.
# Ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: apps-ingress
  annotations:
    ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  - host: app1.dein-training.de
    http:
      paths:
        - path: /
          backend:
            # serviceName: app1 # Vorher
            # Jetzt:
            service:
              name: app1
              port:
                number: 80
  - host: app2.dein-training.de
    http:
      paths:
        - path: /
          backend:
            # serviceName: app2 # Vorher
            # Jetzt:
            service:
              name: app2
              port:
                number: 80
```

```
kubectl apply -f .
# --> Fehler
```

### (Mini-)Schritt 2.6: Wir beheben den letzten Fehler 




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
