---
revealOptions:
  transition: fade
---

# Containerisierung mit Docker

---

## Container vs. Virtual Machine

----

### Betriebssystemvirtualisierung

* VirtualBox, Hyper-V Server, QEMU
Note: Bild einfügen

----

### Containervirtualisierung

* Taskrunner (ähnlich systemd/init)
* Isolieren von Anwendungen und deren Abhängigkeiten durch eigenes Dateisystem

---

## Erste Schritte

```bash
docker run hello-world
```

----

## Erste Schritte

<iframe width="100%" src="http://localhost:4200?u=trainer&p=trainer"> <!-- .element: class="fragment" -->

----

## Docker CLI

Docker CLI ist ein Komandozeilen-Tool mit dem sich auf einfachste weise der docker daemon kontrollieren lösst.

Dies ist vergleichar mit systemd.

Es ist möglich container zu
* starten
* stoppen
* überwachen
* erstellen

----

## Docker CLI

<iframe width="100%" src="http://localhost:4200?u=trainer&p=trainer"> <!-- .element: class="fragment" -->

----

## Docker CLI

```shell
docker run -p -d 3000:3000 gitea/gitea
docker ps
docker logs <container>
```

----

### Übung gitea starten

 - Starte "gitea" vom Docker-Image "gitea/gitea" und exponiere den internen Port 3000 auf den externen Port 80!
 - Zeige alle laufenden Docker-Prozesse an und erkenne, ob der Port 3000 exponiert ist!
 - Gibt es noch andere Ports in dem gitea-Container die nicht exponiert sind? Wenn ja, exponiere auch diesen Port!
 - Betrachte die Log-Ausgabe des gitea-Containers in Echtzeit!
 - Beende den gitea-Container, ohne ihn zu löschen und starte ihn wieder!
 - Lösche den gitea-Container!


### Zusammenfassung

Docker CLI

```
docker help
```

Grundlegendes starten stoppen von containern mit `docker start && docker stop`
Übersicht mit `docker ps` und ausgabe von logs `docker logs`

---

## Docker Architektur

Layers: Top Buttom

* Client
  * Manages:
    * container
    * images
    * networks
    * data volumes
* Rest API
* docker daeomon (server)

----

## Ports, Volumes und Environment Variablen

(random vs fixed)
docker run -d -p 3306 mysql

docker run -d -p 3306:3306 mysql

 docker -v
  (anonymous, named vs path) rw ro etc.
 docker -e root_password

 TODO: mysql image vorbereiten
 TODO: registry vorbereiten

----

### Übung MariaDB starten und einrichten

 - Starte einen [mariaDB](https://hub.docker.com/_/mariadb/) Docker-Container mit:
   - vorgeingestelltem "root"-Passwort (Umbgebungsvariable MYSQL_ROOT_PASSWORD)
   - einer automatisch erstellten Datenbank mit dediziertem Benutzeraccount (Umgebungsvariablen MYSQL_DATABASE, MYSQL_USER, MYSQL_PASSWORT)
 - Sorge dafür, dass das Datenverzeichnis der Datenbank (/var/lib/mysql) auf ein lokales Volume (./volumes/db) gemappt ist!

---

## Container verknüpfen

- beide container starten und gitea die sql verbindung geben

----

### Übung Gitea mit MariaDB verbinden

- Stoppe und lösche nun deinen Gitea Container.
- Konfiguriere den Container so, dass Gitea seine Konfiguration in der lokalen MariaDB speichert
  - Benutze dafür die vorher erstellte Datenbank!

---

## docker-compose (v3)

- motivation, syntax, cli

----

### Übung

- Stoppe und lösche deine vorrangegangen Container ohne Nutzdatemverlust.
- Erstelle eine docker-compose.yml in der [gitea](https://hub.docker.com/r/gitea/gitea/) und mariadb als Services beschrieben sind.
  - Stelle sicher das alle Volumes und Ports erhalten bleiben.
- Lagere das Daten-Verzeichnis von gitea auf deinen Computer aus.

---

## Docker Netzwerke

- docker network ls
- docker-compose naming (netzwerke und container)
- docker-compose -p
- docker network rm
- docker inspect (auch volumes etc.)
- unterschied docker-compose stop/down

----

### Übung

- Füge deiner docker-compose.yml ein "seprates" Netzwerk hinzu!
- Richte nun die Verbindung von gitea und mariaDB über das neuerstellte Netzwerk ein.

---

# Docker Images verstehen und erstellen

---

## Anforderungen an die Anwendung

- alles läuft als Docker-Container
- glusterfs Gegenbeispiel

---

## Docker CLI commit

Unterschied container zu image

```shell
docker run -d gitea/gitea
docker exec -it bash <container id>
```

## Dockerfile an Beispiel eines gegebenen Services

- FROM
- COPY
- CMD

----

### Übung: Service in Docker Einbetten

- Kopiere das Binary (tbd) in den Dockercontainer!
- Stelle sicher, dass der Port 8080 exponiert wird.
- Starte den Container und verbinde dich über localhost:8080

---

## Exkurs

- docker registry erklären vorstellen
- docker hub vorstellen
- docker tags
- docker push
- docker pull

----

### Übung

- Beziehe aus der schulungs-registry einen Container von einem anderem Schulungsmitglied!
- Starte den Container neben deinem bestehenden Dockercontainer auf Port 8081.

---

## Layer und Storage Driver (theorie only)

- Was ist das eigentlich?
- Wo sehe ich das?

---

## Dockerfile Layer

- EXPOSE
- USER
- ENV
- RUN TODO: Bsp einbauen

----

### Übung

- Erweitere deine Dockerfile so, dass die Anwendung nicht mehr unter dem default User und Gruppe läuft!
  - Stelle mit RUN sicher, dass der User berechtigungen hat das Binary zu starten und im Verzeichnis (/app) zu schreiben.
- Konfiguriere die Anwendung über ENV variablen, stelle sicher dass alle Ports exponiert werden.

---

## Advanced Layer

- COPY vs ADD
- WORKDIR
- ENTRYPOINT vs CMD
  - ENTRYPOINT nicht überschreibbar
- HEALTHCHECK ?

----

### Übung

- Versuche den RUN Befehl durch WORKDIR und COPY --chown zu erstezen.

---

## Multistagebuilds

- Konzept vorstellen
  - beispiel an Go Service
- COPY --from
- STOPSIGNAL

----

### Übung

- Baue in einem vorrangestellen Dockercontainer dein Java Jar zusammen, nenne diesen "build"!
  - benutze hierfür Gradle
- Kopiere das erfolgreich gebaute Jar vom ersten Container in den zweiten Container.
  - Nutze hierfür die Docker "Multistage Build"-Funktionalität (COPY --from=build)
- Java Service bauen mit multistage (service tut das gleiche (wie go service) ist in aber in Java geschrieben)

---

## Zusammenfassung Layer, Praxisbeispiel

- Dockerfiles vergleichen
- Layer Vergleichen
- Image Größen vergleichen

---

### Ziel:

- Es existieren zwei Dockerfiles die beide Funktionieren
- ein Go Service ein Java Service
  - der Java service ist selbsterarbeitet

---

## Best practice

- Konfiguration über Umgebungsvariablen
- Logging über STDOUT
  - Filebeat JSONLOG
- Nur ein Prozess
- Exit Codes (SIGTERM usw)

----

### Übung:

- start.sh
  - mit debug help etc
- ENTRYPOINT auf start.sh
- reagiert auf SIGTERM oder definiert ein STOPSIGNAL
- Sinnvolle(tm) Exit-Codes
 (siehe --init)
- Nutze die Health Resource im HEALTHCHECK