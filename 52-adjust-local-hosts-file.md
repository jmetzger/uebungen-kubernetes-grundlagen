# Anpassen Deines lokalen Hosts - Files 

## Schritt 1: EXTERNAL-IP des IngressControllers herausfinden 

```
# Dieser befindet sich im Service des LoadBalancers
# Eintrag unter EXTERNAL-IP 
kubectl -n ingress get svc
# z.B. 

kubectl -n ingress get svc nginx-ingress-ingress-nginx-controller
```

## Schritt 2: hosts - Datei unter windows anpassen

```
WINDOWS - Taste drücken + nach
editor
suchen

oder unten in der Suchleiste nach
editor
suchen
```

![image](https://github.com/jmetzger/uebungen-kubernetes-grundlagen/assets/1933318/e770c313-0a09-4cac-a932-03f06aed5468)

```
# Achtung - Editor als Administrator öffnen
# Rechte Maustaste -> Kontextmenü
Als Administrator öffnen 
```

```
# Dann folgendes Verzeichnis öffnen 
C:\Windows\System32\drivers\etc

# Unten rechts in der Auswahl 
# Alle Dateien
# auswählen

# Dann die "hosts" Datei anklicken 

```

![image](https://github.com/jmetzger/uebungen-kubernetes-grundlagen/assets/1933318/e005f886-4d61-4b13-9d22-ad0077f102de)

```
# jetzt trägst du zusätzlich folgendes unten in die Liste ein
# Achtung: IP-Adresse bitte anpassen, HIER NIMMST Du Deine aus Schritt 1
157.245.25.13  app1.dein-training.de app1
157.245.25.13  app2.dein-training.de app2
```

```
# Danach speicherst du und schliesst den Editor
STRG + S oder im Menü dann oben rechts das Fenster schliessen
```

## Schritt 3: Überprüfen der Domain 

```
# öffne die Linux-Konsole
# und geben
ping -c4 app1.dein-training.de

# Es muss jetzt die IP - Adresse angepingt werden
# und im Idealfall, wenn Deine Firewall das nicht sperrt.
# solltest Du eine Antwort bekommen
```

![image](https://github.com/jmetzger/uebungen-kubernetes-grundlagen/assets/1933318/8f5f56b7-6abf-4028-afe6-1c3c3279b620)

