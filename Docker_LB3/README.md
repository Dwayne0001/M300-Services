***
# Dokumentation LB3 Docker
**Zum Modul 300 von Dwayne Delnevo**
***
## Inhaltsverzeichnis
- [Einleitung](#einleitung)
- [Der Service](#der-service)
- [Technische Angaben](#technische-angaben)
  - [Netzwerkplan](#netzwerkplan)
  - [Code](#code)
  - [Anleitung für den Betrieb](#anleitung-f%C3%BCr-den-betrieb)
- [Testing](#kapitel-3-testing)
- [Quellen](#quellen)
  

***
# Einleitung

In dieser LB geht es darum, einen Service mit Docker aufzusetzten und zu betreiben. Docker ist eine Containervirtualisierungsplatform; Open-Source. Mit Hilfe des Docker Hubs können sehr leicht viele Images (Vorbereitete Zustände von Anwendungen oder OS) heruntergeladen werden.  

# Der Service

Ich werde **Wordpress** als Service verwenden; im Hintergrund realisiere ich **mysql**.  
Dank **PHPmyAdmin** kann amn die Datenbank sehr leicht mit dem Webinterface administrieren. 

Durch die Verlinkung von **MySQL** -> **PHPmyAdmin** und **MySQL** -> **Wordpress** wird dies möglich gemacht.

![GUI](Images/GUI.PNG)

#Technische Angaben

Der Service wird mit 3 Docker Container realisiert. Bei ersten wird MySQL, beim zweiten PHPMyAdmin und beim dritte Wordpress installiert. Durch das custom "Net1" Netzwerk können die Container kommunizieren.

| **Info**           | **Container** 1 |   **Container** 2 |  **Container** 3 |
| :----------------- | :-------------: | ----------------: | ---------------: |
| **Container Name** |      MySQL      |        PHPMyAdmin |        Wordpress |
| **Docker Image**   |    MySQL 5.7    | PHPmyAdmin        | Wordpress        |
| **Netzwerk**       |      Net1       |              Net1 |             Net1 |
| **IP**             |      DHCP       |              DHCP |             DHCP |

## Netzwerkplan

![Netzwerk](Images/netzwerk.png)

Durch das Konfigurieren des Net1 auf "Bridge", werden die Container vom Host erreichbar sein; unter anderem ist der Host dann auch der Gateway ins WAN.  

Per DHCP bekommen die, an das Net1 angehängten, Container eine IP im Range 127.0.0.x/24. Der Gateway/Host hat immer die IP 127.0.0.1.  

Im Abschnitt **Code** wird definiert, wie **PHPmyAdmin** und **Wordpress** auf den **MySQL** Container zugreifen.  

Damit ich vom Host aus auf das Webinterface des **PHPmyAdmin** Zugriff habe, muss der Port 80 des Containers auf den Host Port 8080 gemapt werden. Bei **Wordpress** ähnlich; auf 8081. 
Damit der Host auf das Webinterface des PHPMyAdmin zugreifen kann, muss der Container Port 80 auf den Host Port 8080 gemapt werden. Bei Wordpress wird das gleiche auf den Host Port 8081 gemacht.

## Code
Das Projekt wurde mit einem Docker Compose File realisiert. In diesem File werden alle Container und Netzwerk Parameter definiert. Mit diesem Befehl wird dann die Struktur aufgesetzt:
```Shell
docker-compose -f ʺPfad\zum\File\docker-compose.ymlʺ up -d --build
 ```
"-d" definiert, dass die Container im Hintergrund aufgesetzt wird.

"-f" setzt den den Pfad zum docker-compose.yml File

Hier der Code des docker-compose.yml File:

![code](Images/code.PNG)

Auf Zeile 4-16 wird der MySQL Container erstellt und konfiguriert

Auf Zeile 18-29 wird der PHPMyAdmin Container erstellt. Dabei wird auf Zeile 29 die Verlinkung zwischen PHPMyAdmin und MySQL realisiert(Datenbank = MySQL Container).

Auf Zeile 32-46 wird der Wordpress COntainer erstellt. Auf Zeile 42 wird die Verlinkung zu MySQL gesetzt.

Auf Zeile 47-48 wird das Volume gesetzt.

Auf der Zeile 49-50 wird das Netzwerk erstellt

Das File ist auf meinem GitHub LB2 [Repository][lb2git] abgelegt

## Anleitung für den Betrieb

### 1. Installation <!-- omit in toc -->
Wie oben beim Code erklärt wird per Befehel das Docker-Compose.yml ausgeführt und somit die Container aufgesetzt:
```Shell
docker-compose -f ʺPfad\zum\File\docker-compose.ymlʺ up -d --build
 ```
Wenn alles geklappt hat sieht es so aus:

![installation](Images/installation.PNG)

 ### 2. Zugriff auf MySQL Webinterface <!-- omit in toc -->

- Um auf PHPMyAdmin zuzugreifen, muss ein Browser geöffnet werden.

- Es wird die URL http://localhost:8080 eigegeben

Nun sieht das Fenster so aus:

![Web](Images/web.PNG)

### 3. PHPMyAdmin Login <!-- omit in toc -->
Als Benutzer wird **User** genommen

Das Passwort ist **1234**

Die Anmeldung erflogt beim drücken der Enter-taste

Das Fenster sieht jetzt so aus:

![PHP](Images/GUI.PNG)

### 4. Los arbeiten mit PHPMyAmdin <!-- omit in toc -->

AB jetzt kann mit PHPMyAdmin gearbeitet werden. Es können Datenbanken erstellt und administriert werden.

Wenn benötigt können neue Benutzer angelegt werden.

Offizielle PHPMyAdmin [Website][ophp]

Offizielle MySQL [Website][osql]

### 5. Zugriff auf Wordpress <!-- omit in toc -->

- Um auf Wordpress zuzugreifen, muss ein Browser geöffnet werden.

- Es wird die URL http://localhost:8081 eigegeben

Nun sieht das Fenster so aus:
![Wordpress](Images/wordpress.png)

Jetzt kann die Sprache ausgewählt werden. Anschliessend wird die erste Seite erstellt.

#Testing

Das Testing wir mit einem Testing Protokoll durchgeführt. Dabei wird er SOLL / IST Zustand Verglichen und erläutert wie getestet wurde.

| SOLL-Zustand                                                             |                                      IST-Zustand                                      |                                                                                                            Test |
| :----------------------------------------------------------------------- | :-----------------------------------------------------------------------------------: | --------------------------------------------------------------------------------------------------------------: |
| 3 Container wurden per Befehl installiert                                |                 Die 3 Container wurden erstellt und werden ausgeführt                 | In Powershell wurde der Befehl docker-compose -f "C:\myrep\my_M300\Docker\LB2\docker-compose.yml" up -d --build |
| Das Netzwerk "Net1" wurde erstellt                                       |             Das Netzwerk wurde während dem Ausführen des Befehls erstellt             |                                        Mit dem Befehl: Docker Network ls werden alle Docker Netzwerke angezeigt |
| Die Portverlinkung von PHPMyAdmin von Port 80 auf 8080 ist gewährleistet | Mit http://localhost:8080 kann auf das Webinterface von PHPMyAdmin zugegriffen werden |                                                             Im Browser die Adresse http://localhost:8080 öffnen |
| Mit dem Gesetzten User Login kann man sich anmelden                      |           Mit dem Benutzername User und Passwort 1234 kann eingelogt werden           |                                            In der Anmeldemaske von PHPMyAdmin werden die Login Daten eingegeben |
| Die Portverlinkung von Wordpress von Port 80 auf 8081 ist gewährleistet | Mit http://localhost:8081 kann auf Wordpress zugegriffen werden |                                                             Im Browser die Adresse http://localhost:8081 öffnen |
