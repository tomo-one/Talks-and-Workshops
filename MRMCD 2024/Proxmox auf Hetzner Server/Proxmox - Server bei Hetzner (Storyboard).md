# Storyboard für Proxmox Server bei Hetzner


> [!WARNING] Anpassung erforderlich
> In dem Vortrag habe ich die Domain classroomtux.de und die von Hetzner bereitgestellte IPv4 138.201.192.100 verwendet. Dies muss auf eure Gegebenheiten angepasst werden. Auch der Storagebox User (hier u123456-sub1.your-storagebox.de) muss an euren Usernamen angepasst werden, sofern ihr die Hetzner Storagebox nutzt.

## Die einzelnen Schritte
- Kundenkonto bei Hetzner erstellen, falls noch nicht geschehen.
- Einen neuen dedicated Server oder gebrauchten in der Serverbörse bestellen. Public SSH-Key bei der Bestellung für späteren Zugang verwenden.
- IP Adresse des neuen Systems notieren und booten
- Per SSH mit dem zuvor erstellten SSH-Key in das Rescue System booten.
- Mit `installimage` die  Konfiguration und Installation starten.

## Vorbereitung

### Keypair erstellen
``` bash
ssh-keygen -t ed25519 -f ~/.ssh/mrmcd2024 -C "MRMCD 2024"
```

### Private Key laden
``` bash
ssh-add ~/.ssh/mrmcd2024
```

### Server bei Hetzner bestellen
https://www.hetzner.com/sb/

![](../_Medien/Hetzner%20Server%20Auktion%20-%20Serverdetails.png)

![](../_Medien/Hetzner%20Server%20Auktion%20-%20Bestellzusammenfassung.png)

### Public Key in die Zwischenablage kopieren (MAC)
``` bash
cat ~/.ssh/mrmcd2024.pub | pbcopy
```

### Public Key in die Zwischenablage kopieren (Windows - Eingabeaufforderung)
``` cmd
type .\mrmcd2024.pub | clip
```

### Public Key in die Zwischenablage kopieren (Windows - Powershell)
``` powershell
get-content .\mrmcd2024.pub | clip
```


### Nameserver konfigurieren
```
mrmcd2024.classroomtux.de    A        138.201.192.100
proxmox.classroomtux.de.     CNAME    mrmcd2024.classroomtux.de.
proxymgr.classroomtux.de     A        138.201.192.100
apache.classroomtux.de       A        138.201.192.100
nginx.classroomtux.de        A        138.201.192.100
```

### ssh config erstellen

``` bash
nano ~/.ssh/config.d/mrmcd2024
```

``` nano
# ~/.ssh/config.d/mrmcd2024

Host mrmcd2024 mrmcd2024.classroomtux.de
    HostName mrmcd2024.classroomtux.de
    Port 22
    User root
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/mrmcd2024

Host proxymgr-mrmcd2024
    HostName mrmcd2024.classroomtux.de
    Port 22
    User root
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/mrmcd2024
    LocalForward 9000 10.10.100.252:81

```


### Rescue System booten und Proxmox installieren

#### SSH in das Rescue System
``` bash
ssh mrmcd2024
```

#### Config anpassen

 ``` bash
 installimage
 ```

``` Editor
SWRAID 1

SWRAIDLEVEL 0
 
HOSTNAME mrmcd2024.classroomtux.de

IPV4_ONLY yes

# BTRFS
PART    /boot    ext3       1024M
PART    btrfs.1  btrfs      all

SUBVOL  btrfs.1  @          /
SUBVOL  btrfs.1  @tmp       /tmp
SUBVOL  btrfs.1  @vz        /var/lib/vz
SUBVOL  btrfs.1  @btrfs     /storage

IMAGE /root/.oldroot/nfs/install/../images/Debian-bookworm-...
```

``` bash
reboot
```

### Alten Eintrag aus `~/.ssh/known_hosts` entfernen

``` bash
ssh-keygen -f ~/.ssh/known_hosts -R mrmcd2024.classroomtux.de
```

### SSH testen

``` bash
ssh mrmcd2024
```

### Passwort von root ändern

```
passwd
```

### Alias hinzufügen (optional)

``` bash
nano ~/.bashrc
```

``` nano
alias ll='ls -halF'
```

``` bash
. .bashrc
```

### Proxmox aufrufen

https://mrmcd2024.classroomtux.de:8006/

### Let's Encrypt für Proxmox einrichten

[Proxmox - Let's Encrypt einrichten](Proxmox%20-%20Let's%20Encrypt%20einrichten.md)

### Local Storage disablen

`Datacenter / Storage / local` disablen

#### Neuen lokalen Storage als BTRFS anlegen

![](../_Medien/Proxmox%20-%20BTRFS%20für%20MRMCD%20hinzufügen.png)

``` text
ID:         mrmcd
Path:       /storage/mrmcd


Content:    Disk Image, ISO Image, Container Template, VZdump backup file, Container, Snippets
```


#### SMB Share zur Storagebox (optional)

![](../_Medien/Proxmox%20-%20Storagebox%20für%20Backup%20hinzufügen.png)

``` text
ID:             storagebox
Server:         u123456-sub1.your-storagebox.de
Username:       u123456-sub1
Password:       <Das in der Storagebox vergebene Passwort>
Share:          u123456-sub1
Domain:         
Subdirectory:   

Content:        ISO Image, Container Template, VZdump backup file
```


##### Storage prüfen

``` bash
ll /storage/mrmcd/
```

``` bash
ll /mnt/pve/storagebox/
```

#### Backup Files von Storagebox zu lokal kopieren (sofern Backups auf der Storagebox vorhanden)

``` bash
# In das lokale Backup-Verzeichnis wechseln
cd /storage/mrmcd/dump
```

``` bash
# tmux installieren und ausführen
apt install tmux -y
tmux
```

``` bash
# Proxymanager
cp /mnt/pve/storagebox/dump/vzdump-lxc-100252-2024_09_29-21_49_26.* .
```

``` bash
# Apache PHP Template
cp /mnt/pve/storagebox/dump/vzdump-lxc-172110-2024_09_29-20_01_21.* .
```

``` bash
# Nginx PHP Template
cp /mnt/pve/storagebox/dump/vzdump-lxc-190100-2024_09_29-20_01_47.* .
```

``` bash
# Password Cracking
cp /mnt/pve/storagebox/dump/vzdump-lxc-170100-2024_09_29-19_01_05.* .
```

``` bash
# LinuxLite Training
cp /mnt/pve/storagebox/dump/vzdump-qemu-170080-2024_09_29-19_04_00.* .
```


#### Netzwerkkonfiguration
``` bash
nano /etc/network/interfaces
```

``` nano
# Interface Namen prüfen und ggf. anpassen. Hier "enp0s31f6".

source /etc/network/interfaces.d/*

auto lo
iface lo inet loopback

auto enp0s31f6
iface enp0s31f6 inet static
        address 138.201.192.100/26
        gateway 138.201.192.65
        
        post-up    echo 1 > /proc/sys/net/ipv4/ip_forward


        # Erst auskommentieren, wenn der Proxymanager installiert ist
        # UND die Let's Encrypt Zertifikate installiert sind

        post-up    iptables -t nat -A PREROUTING -i enp0s31f6 -p tcp -m multiport ! --dport 8006,22,81 -j DNAT --to 10.10.100.252
        post-up    iptables -t nat -A PREROUTING -i enp0s31f6 -p udp -j DNAT --to 10.10.100.252



# Infrastructure Network
auto vmbr100
iface vmbr100 inet static
    address 10.10.100.254/24
    bridge-ports none
    bridge-stp off
    bridge-fd 0

    post-up    iptables -t nat -A POSTROUTING -s '10.10.100.0/24' -o enp0s31f6 -j MASQUERADE
    post-down  iptables -t nat -D POSTROUTING -s '10.10.100.0/24' -o enp0s31f6 -j MASQUERADE


# Network 170
auto vmbr170
iface vmbr170 inet static
    address 10.10.170.254/24
    bridge-ports none
    bridge-stp off
    bridge-fd 0

    post-up    iptables -t nat -A POSTROUTING -s '10.10.170.0/24' -o enp0s31f6 -j MASQUERADE
    post-down  iptables -t nat -D POSTROUTING -s '10.10.170.0/24' -o enp0s31f6 -j MASQUERADE


# Network 172
auto vmbr172
iface vmbr172 inet static
    address 10.10.172.254/24
    bridge-ports none
    bridge-stp off
    bridge-fd 0

    post-up    iptables -t nat -A POSTROUTING -s '10.10.172.0/24' -o enp0s31f6 -j MASQUERADE
    post-down  iptables -t nat -D POSTROUTING -s '10.10.172.0/24' -o enp0s31f6 -j MASQUERADE


#Network 190
auto vmbr190
iface vmbr190 inet static
    address 10.10.190.254/24
    bridge-ports none
    bridge-stp off
    bridge-fd 0

    post-up    iptables -t nat -A POSTROUTING -s '10.10.190.0/24' -o enp0s31f6 -j MASQUERADE
    post-down  iptables -t nat -D POSTROUTING -s '10.10.190.0/24' -o enp0s31f6 -j MASQUERADE
```

``` bash
reboot
```

#### Backups restoren
![](../_Medien/Proxmox%20-%20Backups%20für%20MRMCD2024%20Talk.png)

#### LXC Container in der Bash restoren
``` bash
# LXC Container Restore
pct restore 100252 /storage/mrmcd/dump/vzdump-lxc-100252-2024_09_29-21_49_26.tar.zst
pct restore 170100 /storage/mrmcd/dump/vzdump-lxc-170100-2024_09_29-19_01_05.tar.zst
pct restore 172110 /storage/mrmcd/dump/vzdump-lxc-172110-2024_09_29-20_01_21.tar.zst
pct restore 190100 /storage/mrmcd/dump/vzdump-lxc-190100-2024_09_29-20_01_47.tar.zst
```

#### VM in der Bash restoren
``` bash
# VM Disk Restore
qmrestore /storage/mrmcd/dump/vzdump-qemu-170080-2024_09_29-19_04_00.vma.zst 170080
```

#### Proxymanager erreichbar machen und konfigurieren

##### Via Linux System auf Proxmox
![](../_Medien/Proxymanager%20-%20Proxy%20Host%20für%20classroomtux.de.png)

##### Via SSH Tunnel

###### Mit entsprechender .ssh/config
``` bash
ssh proxymgr-mrmcd2024
```

###### Ohne .ssh/config von der Bash aus
``` bash
ssh root@mrmcd2024.classroomtux.de -L 9000:10.10.100.252:81
```

Auf dem lokalen Rechner aufrufen: http://localhost:9000

![](../_Medien/Proxymanager%20für%20MRMCD2024.png)

| Source                   | Schema | Host          | Port |
| ------------------------ | ------ | ------------- | ---- |
| apache.classroomtux.de   | http   | 10.10.172.110 | 80   |
| nginx.classroomtux.de    | http   | 10.10.190.100 | 80   |
| test1.classroomtux.de    | https  | github.com    | 443  |

##### Via Proxymanager
Einen Eintrag im DNS vornehmen und auf den Proxymanager Port 81 weiterleiten.

| Source                   | Schema | Host          | Port |
| ------------------------ | ------ | ------------- | ---- |
| proxymgr.classroomtux.de | http   | 10.10.100.252 | 81   |

Auf dem lokalen Rechner aufrufen: https://proxymgr.classroomtux.de/

