# Erstelle einen NFS-Server auf DigitalOcean 

## Step 1: Finde das private IP-Netz heraus (in dem Dein Cluster bei Digitalocean läuft) 

```
# In der Linux Console (z.B. WSL) 
kubectl get nodes -o wide
# z.B. 10.135.0.x
# Bei Dir wird das ein anderes Netz sein.
```

## Step 2: Erstelle lokal eine Public/PrivateKey - Schlüssel - Kombination 

```
# In der Linux Console (z.B. WSL)
ssh-keygen 

# Dialog: Enter file in which to save the key -
# Default: id_rsa
# Drücke hier die Taste ENTER/RETURN 

# Enter passphrase
# Gebe hier ein Passphrase ein, um den privaten key abzusichern .
# Gebe danach enter ein.

# Es wird ein Schlüsselpaar erstellt (public/private key) 

# Folgenden public brauchen wir später für Digitalocean
cat .ssh/id_rsa.pub 

```


## Step 3a: Erstelle ein neues Droplet (eine virtuelle Maschine über das Webinterface) 

```
# Create Droplets

## Settings

### Choose Region

Frankfurt

### Choose an Image

Ubuntu -> Latest LTS -> e.g. 22.04 LTS

### Choose Type

#### Droplet Type

Shared CPU -> Basic

#### CPU Options

Regular
1 GB / 1 CPU / 25 GB SSD Disk  

### Choose authentication method

SSH Key
Klicke den Button (wenn Du nicht schon einen ssh-key dort hast):
NEW SSH Key

### Im PopUp-Fenster

Kopiere hier das was auf der Konsole mit
cat .ssh/id_rsa.pub
siehst, in das Feld: SSH key content

Gebe bei Namen einen beliebigen Namen für Deinen Key an:
z.B. Jochens-Key

Klicke den Button:
Add SSH Key
```


## Schritt 3b: neues Droplet (Fortsetzung) 

```
### Klicke unter: We recommend these options -> + Advanced Options

#### Klicke "Add Initialization scripts (free)

... so dass es vorausgewählt ist.
```

```
#### Füge dort folgenden Text ein
```

```
#!/bin/bash

# Ändere dies in Deine eigene aus Schritt 1
IP_RANGE="10.135.0.0" 

groupadd sshadmin
usermod -aG sshadmin root
echo "AllowGroups sshadmin" >> /etc/ssh/sshd_config 
systemctl reload sshd 

# update repo 
apt-get update 
apt-get install -y nfs-kernel-server

# Prüfen Create folder structure 
mkdir -p /var/nfs/nginx/html 

### Show exports file 
echo "/var/nfs $IP_RANGE/24(rw,sync,no_root_squash,no_subtree_check)" >> /etc/exports;
exportfs -av
```

## Schritt 3c: Vergebe einen Hostname unter hostname

```
# Der Name ist beliebig, wir brauchen ihn später nicht, z.B.
# Die Domain wird NICHT ! gegen die IP aufgelöst 
nfs.training.abc
```

## Schritt 3d: Klicke den Button "Create Droplet" 

```
# wir warten jetzt bis das Droplet bereit ist
```

## Schritt 4: Überprüfen, ob alles passt.

```
# Wir loggen uns über die Console in der Web-GUI ein
# Dann kommen wir als root rein
# prüfen, ob das Verzeichnis vorhanden ist.
ls -la /var/nfs/nginx/html
# Prüfen, ob der nfs-server läuft
systemctl status nfs-kernel-server
# Nachschauen, ob die IP-Range auch richtig eingetragen wurde.
exportfs -av
```


