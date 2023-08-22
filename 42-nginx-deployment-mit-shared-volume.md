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
apiVersion: v1
kind: PersistentVolume
metadata:
  # any PV name
  name: pv-nfs-tln<nr>
  labels:
    volume: nfs-data-volume-tln<nr>
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
    path: /var/nfs/tln<nr>/nginx
    server: 10.135.0.18
    readOnly: false
  storageClassName: ""

```

## Schritt 2: PersistentVolumeClaim erstellen 

```


```

## Schritt 3: Volume und VolumeMounts in Deployment einrichten 

```


```
