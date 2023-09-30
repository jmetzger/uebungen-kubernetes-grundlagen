# Bash Completion 

## Schritt 1:

```
# Als Benutzer training ausführen 
kubectl completion bash | sudo tee /etc/bash_completion.d/kubectl > /dev/null

# oder in den root-Benutzer wechseln
sudo su -
kubectl completion bash > /etc/bash_completion.d/kubectl
exit 
```

## Schritt 2: Für Benutzer "aktivieren"

```
# das hat den gleichen Effekt, als wie wenn wir uns neu einloggen 
# Dadurch wird die Completion erst aktiv

su - training 
```

## Schritt 3: Testen 

```
# kubectl cl eingeben und dann die TAB - Taste drücken 
kubectl cl<TAB>
```
