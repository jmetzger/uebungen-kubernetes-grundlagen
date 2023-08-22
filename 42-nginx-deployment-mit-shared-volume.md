# Nginx Deployment mit Shared Volumed 

## Voraussetzungen

  * In manifests/03-deploy/app1 sollten service.yml und deploy.yml vorhanden sein
  * Falls nicht: Bitte nochmal zur Lektion zum Einrichten des Services und des Deployments zurückgehen
  * <INTERNE-IP-NFS-SERVER> aus der vorigen Übung 

## Vorbereitung 

```
cd manifests
cd 03-deploy/app1
```

## Schritt 1: PersistentVolume erstellen 

```
# Interne IP des NFS-Servers in DigitalOcean herausfinden
# z.B. 10.135.0.7 
```

![image](https://github.com/jmetzger/uebungen-kubernetes-grundlagen/assets/1933318/f07e28fd-457c-477b-9f18-044b0ab562ea)

```
nano 01-pv.yml
```

```
apiVersion: v1
kind: PersistentVolume
metadata:
  # any PV name
  name: pv-nfs
spec:
  capacity:
    # storage size
    storage: 1Gi
  accessModes:
    # ReadWriteMany(RW from multi nodes), ReadWriteOnce(RW from a node), ReadOnlyMany(R from multi nodes)
    - ReadWriteMany
  persistentVolumeReclaimPolicy:
    # retain even if pods terminate
    Retain
  nfs:
    # NFS server's definition
    path: /var/nfs/nginx/html
    server: <INTERNE_IP_NFS_SERVER> # !! ACHTUNG HIER KOMMT DIE INTERNE-IP Deines NFS-Servers rein.
    readOnly: false
  storageClassName: ""
```

```
kubectl apply -f 01-pv.yml 
kubectl get pv 
```

## Schritt 2: PersistentVolumeClaim erstellen 

```
# vi 02-pvc.yml 
# now we want to claim space
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-nfs-claim
spec:
  storageClassName: ""
  volumeName: pv-nfs
  accessModes:
  - ReadWriteMany
  resources:
     requests:
       storage: 1Gi
```

```
kubectl apply -f 02-pvc.yml
kubectl get pvc
```

## Schritt 3: Volume und VolumeMounts in Deployment einrichten 

```
nano deploy.yml 
```

```
# Ergänzen des Deployments, so dass dieses folgendermaßen aussieht.
# nano deploy.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 8 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.24
        ports:
        - containerPort: 80
        # AB HIER IST DER NEUE TEIL
        volumeMounts:
          - name: nfsvol
            mountPath: "/usr/share/nginx/html"

    volumes:
      - name: nfsvol
        persistentVolumeClaim:
          claimName: pv-nfs-claim
```

```
kubectl apply -f deploy.yaml
```

## Schritt 4: Service anpassen

```
nano service.yml
```

```
# Wir löschen oder kommentieren die Zeile: type: LoadBalancer
# Wir brauchen nur eine ClusterIP
spec:
  # type: LoadBalancer # Not needed, because we only need type: ClusterIP which is default 
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: nginx
```

```
kubectl apply -f service.yml
```

## Schritt 5: Daten in NFS schreiben und testen 

```
# Eine index.html im Container hinzufügen   
kubectl exec -it deploy/nginx-deployment -- bash 
```

```
# Im Container 
echo "Hallo, junger Padavan" > /usr/share/nginx/html/index.html 
exit 
```

```
# ClusterIP herausfinden  
kubectl get svc 
```

```
# Pod zum Testen hochfahren 
kubectl run -it --rm podtester --image=busybox
```

```
# Im container des Pods "podtester" 
wget -O - http://<ClusterIP>
exit
```

```
# Deployment zerstören  
kubectl delete -f deploy.yml 
```

```
# Nochmal  
kubectl run -it --rm podtester --image=podtester  
```

```
# Keine Verbindung 
wget -O - http://<ClusterIP>
exit
```

## Schritt 6: ReDeployen. Sind Daten wieder da ?

```
# now start deployment again 
kubectl apply -f deploy.yml 

# and try connection again  
kubectl run -it --rm podtester --image=busybox  
```

```
# Im Container des Pods podtester
wget -O - http://<ClusterIP>
# exit 
```
