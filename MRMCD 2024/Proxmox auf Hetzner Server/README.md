# Proxmox Server bei Hetzner: Mehrere interne Netzwerke und nur eine öffentliche IP-Adresse

| Art und Dauer         | Online-Vortrag (90 Minuten)                                                                                             |
| :-------------------- | :---------------------------------------------------------------------------------------------------------------------- |
| Termin:               |                                                                                                                         |
| Talkbeschreibung:     | https://talks.mrmcd.net/2024/talk/FHMMQW/                                                                               |
| Aufzeichnung CCC:     | https://media.ccc.de/v/2024-408-proxmox-server-bei-hetzner-mehrere-interne-netzwerke-und-nur-eine-ffentliche-ip-adresse |
| Aufzeichnung Youtube: | https://www.youtube.com/watch?v=2H4N9ng3KOk                                                                             |

## Zusammenfassung
Hetzner macht es besonders leicht, auf einem angemieteten Server Proxmox zu installieren. Dazu muss man lediglich das bereitgestellte Proxmox Image bei der Installation aus dem Rescue System auswählen.

Etwas herausfordernder wird es, wenn man mehrere virtuelle Server auf Proxmox installieren und dem Rest der Welt zugänglich machen will - was ja vermutlich der Grund für die Auswahl und Installation von Proxmox war.

Bei einem Proxmox im Heimnetzwerk ist es recht simple: Man gibt dem Dienst einfach eine freie IP-Adresse aus dem verwendeten privaten Netzwerk. Bei einem Internetanbieter muss ich üblicherweise für jeden per IPv4 erreichbaren Dienst eine IP Adresse dazu kaufen. Das kann schnell teuer werden. Und bei einigen Anbietern bekomme ich auch nur eine begrenzte Anzahl an IPv4 Adressen.

Eine kostengünstige Variante ist, die virtuellen Server mit privaten Adressen zu versorgen und auf dem Host zu natten (Destination NAT). Für die Weiterleitung an das richtige Ziel kann man zum Beispiel VyOS (virtueller Router) oder den NGINX Proxymanager verwenden. Ihr ahnt es bereits - wir verwenden hier den NGINX Proxymanager.

## Motivation
Die Youtube Videos zu diesem Thema hatten mich damals nicht überzeugt beziehungsweise waren die Lösungen nicht skalierbar. Aus diesem "Mangel" heraus ist dieses Setup und diese Konfiguration entstanden. Ich verwende sie inzwischen seit mehreren Jahren für Trainings und Workshops für Cyber Security Professionals. Auch meine eigenen Server betreibe ich nach dieser Art ohne Probleme.


## Beschreibung
In diesem Talk werden wir auf einem Hetzner Dedicated Server ein Proxmox System von Scratch installieren, das bedeutet:
- Booten in das Rescue System
- Auswahl des Images
- Konfigurieren des Setup Scripts (installimage)
- Update von Proxmox auf die aktuelle Version
- anpassen der Proxmox Netzwerkkonfiguration (mehrere Netzwerke)
- Einbinden der Hetzner Storagebox
- Installation einiger Server und Dienste
- Konfiguration des DNS
- Installation und Konfiguration des NGINX Proxymanagers

Dieser Talk ist als Demo angelegt. Ich werde alle Schritte live ausführen und am Ende werden wir eine funtkionierende Proxmox Installation mit internen Netzwerken und NGINX Proxymanger haben. Der Talk wird aufgezeichnet, so dass alle Schritte später selber ausgeführt werden können.


## Enthaltene Dateien

| Datei                            | Link                                                                                                                                          |
| :------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------- |
| Präsentation des Vortrags (PDF): | [Proxmox auf Hetzner Server - 2024-10-04 (DE)](Proxmox%20auf%20Hetzner%20Server/Proxmox%20auf%20Hetzner%20Server%20-%202024-10-04%20(DE).pdf) |
| Storyboard:                      | [Proxmox - Server bei Hetzner (Storyboard)](Proxmox%20auf%20Hetzner%20Server/Proxmox%20-%20Server%20bei%20Hetzner%20(Storyboard).md)          |
| Let's Encrypt einrichten:        | [Proxmox  - Let's Encrypt einrichten](Proxmox%20auf%20Hetzner%20Server/Proxmox%20-%20Let's%20Encrypt%20einrichten.md)                         |


## Lizenz
Creative Commons BY-NC-ND
