# Let's Encrypt für Proxmox einrichten

## Requirements
- Domain Name
- DNS Records
- Email Adresse für Proxmox ACME Account

> [!WARNING] ACHTUNG
> Ggf. Portweiterleitungen (zum Proxymanager) in `/etc/network/interfaces` vorher deaktivieren, da Letsencrypt den Port 80 benötigt.


## ACME Accounts unter Proxmox anlegen (im Datacenter)
Erst den Staging, dann den normalen Account anlegen.

![](../_Medien/Proxmox%20-%20ACME%20Account.png)


## Domain hinzufügen (im Node)

Wechsel zum Proxmox-Node und dort System/Certificates auswählen.

Mit "Edit" das zu verwendende Konto auswählen.

![](../_Medien/Proxmox%20-%20ACME%20Account%20Auswahl.png)


Unter `NODE / System / Certificates` das soeben erstellte Plugin und den Domainnamen hinzufügen.

![](../_Medien/Proxmox%20-%20Domain%20für%20Let's%20Encrypt%20hinzufuegen%20(HTTP).png)

## Certificate "bestellen"

Domain auswählen und für "Staging" auf "Order Certificate Now" klicken.
![](../_Medien/Proxmox%20-%20order%20Let's%20Encrypt%20certificate%20(Staging).png)


Wenn alles funktioniert hat nochmal mit dem normalen Account bestellen.

![](../_Medien/Proxmox%20-%20order%20Let's%20Encrypt%20certificate.png)


> [!WARNING] ACHTUNG
> Ggf. Portweiterleitungen (zum Proxymanager) in `/etc/network/interfaces` wieder aktivieren.

