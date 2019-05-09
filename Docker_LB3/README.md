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

![PHPmyAdmin](PHPmyAdminNEW.PNG)

# Technische Angaben

Der Service wird mit 3 Docker Container realisiert. Bei ersten wird MySQL, beim zweiten PHPMyAdmin und beim dritte Wordpress installiert. Durch das custom "Net1" Netzwerk können die Container kommunizieren.

| **Info**           | **Container** 1 |   **Container** 2 |  **Container** 3 |
| :----------------- | :-------------: | ----------------: | ---------------: |
| **Container Name** |      MySQL      |    PHPMyAdmin     |     Wordpress    |
| **Docker Image**   |    MySQL 5.7    |    PHPmyAdmin     |     Wordpress    |
| **Netzwerk**       |      Net1       |        Net1       |        Net1      |
| **IP**             |      DHCP       |        DHCP       |        DHCP      |

## Netzwerkplan

![Netzwerkplan](Netzwerkplan.PNG)  
Das mit Blau markierte Netzwerk ist das Dockernetzwerk welches sich im 127.0.0.x/24 Bereich befindet.

Durch das Konfigurieren des Net1 auf "Bridge", werden die Container vom Host erreichbar sein; unter anderem ist der Host dann auch der Gateway ins WAN.  

Per DHCP bekommen die, an das Net1 angehängten, Container eine IP im Range 127.0.0.x/24. Der Gateway/Host hat immer die IP 127.0.0.1.  

Im Abschnitt **Code** wird definiert, wie **PHPmyAdmin** und **Wordpress** auf den **MySQL** Container zugreifen.  

Damit ich vom Host aus auf das Webinterface des **PHPmyAdmin** Zugriff habe, muss der Port 80 des Containers auf den Host Port 8080 gemapt werden. Bei **Wordpress** ähnlich; auf 8081.  

## Code
Das Projekt wurde mit Hilfe eines Docker Compose Files erstellt. Alle Netzwerk und Container Parameter werden dabei definiert.  
Mit folgendem Befehl wird die ganze Struktur aufgesetzt:  
```Shell
docker-compose -f ʺPfad\zum\File\docker-compose.ymlʺ up -d --build
 ```
Durch "-d" wird definiert, dass die Container im Hintergrund aufgesetzt wird.

Durch "-f" setzt man den Pfad zum docker-compose.yml File

Hier der Code des Docker Compose Files:

![Code](Code.PNG)

**Auf Zeile 4-16** wird der MySQL Container erstellt und konfiguriert

**Auf Zeile 18-29** wird der PHPMyAdmin Container erstellt. Dabei wird auf Zeile 29 die Verlinkung zwischen PHPMyAdmin und MySQL realisiert(Datenbank = MySQL Container).

**Auf Zeile 32-46** wird der Wordpress COntainer erstellt. Auf Zeile 42 wird die Verlinkung zu MySQL gesetzt.

**Auf Zeile 47-48** wird das Volume gesetzt.

**Auf der Zeile 49-50** wird das Netzwerk erstellt

Das File ist in meinem Repository abgelegt.  

## Anleitung für den Betrieb

### 1. Installation  
Mit Hilfe des oben beschriebenen Codes werden per dem hier unten beschriebenem Befehl die Container aufgesetzt:  
```Shell
docker-compose -f ʺPfad\zum\File\docker-compose.ymlʺ up -d --build
 ```
Am Ende einer erfolgreichen Ausetzung, sollte es so aussehen.  

![Installation](Installation.PNG)

 ### 2. Zugriff auf MySQL Webinterface  

- Um auf PHPmyAdmin zugreifen zu können, muss man auf irgendeinen Browser (von der Host-Maschine) die URL: http://localhost:8080 eingeben.  

Folgend öffnet sich dieses Fenster:  

![phpLogin](phpLogin.PNG)

### 3. PHPMyAdmin Login  
- Es wurde der Benutzer **testuser** definiert (im Skript) und

- Das Passwort lautet **test312**

- Nach erfolgreichem Anmelden, sieht man nun das PHPmyAdmin Webinterface.  

![AdminWeb](AdminWeb.PNG)

### 4. Los arbeiten mit PHPMyAmdin 

Ab sofort ist es möglich mit PHPmyAdmin zu arbeiten und Datenbanken zu erstellen oder zu administrieren.  

Falls gewünscht, können natürlich auch weitere Benutzer angelegt werden.  

Offizielle PHPMyAdmin [Website][ophp]

Offizielle MySQL [Website][osql]

### 5. Zugriff auf Wordpress 

- Wie mit PHPmyAdmin, muss man bei Wordpress auch einen Browser auf der Host-Maschine öffnen und den Link: http://localhost:8081 (8081 Wichtig!) eingeben.  

# Testing

Das Testing wir mit einem Testing Protokoll durchgeführt. 
Dabei wird der SOLL / IST Zustand Verglichen und erläutert wie er getestet wurde.

| SOLL-Zustand                                                             |                                      IST-Zustand                                      |                                                                                                            Test |
| :----------------------------------------------------------------------- | :-----------------------------------------------------------------------------------: | --------------------------------------------------------------------------------------------------------------: |
| 3 Container werden per Befehl installiert.                               |                 Die 3 Container wurden erstellt und werden ausgeführt.                | In Powershell wurde der Befehl docker-compose -f "C:\myrep\my_M300\Docker\LB2\docker-compose.yml" up -d --build angewendet. |
| Das Netzwerk "Net1" wird erstellt.                                       |             Das Netzwerk wird während dem Ausführen des Befehls erstellt.            |                                        Mit dem Befehl: Docker Network ls werden alle Docker Netzwerke angezeigt. |
| Die Portverlinkung von PHPMyAdmin, Port 80 auf 8080, ist gewährleistet. | Mit http://localhost:8080 kann auf das Webinterface von PHPMyAdmin zugegriffen werden. |                                                             Im Browser die Adresse http://localhost:8080 öffnen. |
| Mit dem Gesetzten User Login kann man sich anmelden.                     |           Mit dem Benutzername testuser und Passwort test312 kann man sich eingen.           |                                            In der Anmeldemaske von PHPMyAdmin werden die Login Daten getestet. |
| Die Portverlinkung von Wordpress von Port 80 auf 8081 ist gewährleistet. | Mit http://localhost:8081 kann auf Wordpress zugegriffen werden. |                                                             Im Browser die Adresse http://localhost:8081 öffnen. |
