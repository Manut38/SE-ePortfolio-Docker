# ePortfolio - Docker / Portainer / Traefik
## von Manuel Piroch

Dieses Repository beinhaltet die Präsentation des ePortfolios als PDF, sowie die `docker-compose.yml` Datei für die Erstellung der Traefik Instanz.

Um den Schritten zu folgen, bitte dieses Repo clonen oder als ZIP herunterladen und in einen Ordner entpacken.

# Anleitung

## Installation von Docker

Die Installation von Docker erfordert eine beliebige Linux Distribution (Ich empfehle Ubuntu oder Debian) oder Microsoft Windows 10 Pro / Enterprise als zugrunde liegendes Betriebssystem.

Alternativ lässt sich Docker auch unter Windows 10 **Home** unter der (zum Zeitpunkt des Verfassens) aktuellsten Insider Preview per WSL2 zum Laufen bringen.<br>
Hierfür ist mindestens die Windows 10 Version **2004** erforderlich.

### Links

[Installation unter Windows 10 Pro / Enterprise](https://docs.docker.com/docker-for-windows/install/)

[Installation unter Windows 10 Home (Version >= 2004)](https://docs.docker.com/docker-for-windows/install-windows-home/)

[Installation unter Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

[Installation unter Debian](https://docs.docker.com/engine/install/debian/)

## Deployment von Portainer

Nach erfolgreicher Installation von Docker kann nun jedes beliebige software image aus dem [Docker Hub](https://hub.docker.com/) ziemlich einfach deployed werden.

Für die Installation der Web-Management Oberfläche Portainer sind so nur die folgenden zwei Befehle notwendig.

`$ docker volume create portainer_data`<br>
(Legt einen persistenten Datenort an, der von verschiedenen Containern geteilt werden kann, und  einen Neustart des Containers überlebt.)


`$ docker run -d -p 9000:9000 -p 8000:8000 --name portainer --restart always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer`
Läd das Portainer Image herunter und startet es innerhalb eines neuen Containers.

### Erläuterung der Flags
- `-p <container_port>:<host_port>`<br>
  Veröffentlicht einen internen Port des Containers auf einen des Hosts. Im Falle von Portainer befindet sich hier die Web Oberfläche, die dann auf der gleichen Maschine über http://localhost:9000/ aufgerufen werden kann.

- `--restart always`<br>
  Erlaubt es dem Container, automatisch nach einem neustart der Docker Instanz, bzw. eines Serverneustarts, sich selbst erneut hochzufahren.

- `-v /var/run/docker.sock:/var/run/docker.sock`<br>
  Gibt dem Container Zugriff auf den Docker Socket, womit Portainer mit der Docker Instanz interagieren kann.

- `-v portainer_data:/data`<br>
  Gibt dem Container Zugriff auf das zuvor angelegte Volume für persistente Datenspeicherung.

## docker-compose

Im Ordner `traefik` befindet sich die `docker-compose.yml` womit sich die Traefik Instanz und eine Applikation namens `whoami` starten lässt.

Hierfür in diesen Ordner wechseln und das folgende Kommando ausführen.

`docker-compose up -d`

Dies erstellt Container, deren Parameter in der `docker-compose.yml` definiert sind.

## Reverse-Proxy mit Traefik

Traefik arbeitet dabei als Reverse-Proxy und veröffentlicht den `whoami` service unter der URL http://whoami.localhost/

Hierfür verantwortlich sind die folgenden Zeilen der `docker-compose.yml`:

```
labels:
    - "traefik.http.routers.whoami.rule=Host(`whoami.localhost`)"
```