# WOL
WOL Setup mit Hilfe von systemd.
Client sendet WOL Signal an Server. Server fährt automatisch über Nacht herunter.


## Prämisse:
Client und Server müssen sich im selben Subnetz befinden.
WOL fähiges Mainboard im Server.


## Automatisches Herunterfahren über systemd Serverseitig:
* Installieren von ethtool
```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt install ethtool
```
* Option 1: Automatisches Herunterfahren
```
nano /etc/systemd/system/nachtschaltung.service
```
```
[Unit]
Description=Automatisches Herunterfahren

[Service]
WorkingDirectory=/root/
Type=oneshot
ExecStart=/bin/bash nachtschaltung.sh

[Install]
WantedBy=basic.target
```


```
nano nachtschaltung.sh
```
```
shutdown 23:30 "Automatisches Herunterfahren"
```

```
systemctl enable /etc/systemd/system/nachtschaltung.service
```
```
systemctl start nachtschaltung.service
```
```
systemctl status nachtschaltung.service
```
* Wake-On: g
```
ethtool (enps60/Netzwerkname)
```
```
nano /etc/systemd/system/wol-g.service
```
```
[Unit]
Description=Wake-On: g
After=network-online.target

[Service]
Type=oneshot
ExecStart=/sbin/ethtool -s enp6s0 wol g

[Install]
WantedBy=basic.target
```

```
systemctl enable /etc/systemd/system/wol-g.service
```
```
systemctl start wol-g.service
```
```
systemctl status wol-g.service
```


* MAC Adresse notieren:

```
ip addr
```
## Vorbereitung des Clients:
* Installieren von ethtool
```
sudo apt update && sudo apt upgrade -y
```
```
sudo apt install ethtool
```
```
sudo nano node1.sh
```
```
wakeonlan MAC ADRESS
```
* Setzen des Aufwach-Service
```
sudo nano /etc/systemd/system/node1.service
```
```
[Unit]
Description=Server starten

[Service]
WorkingDirectory=/root/
Type=oneshot
ExecStart=/bin/bash node1.sh
KillMode=process

[Install]
WantedBy=multi-user.target
```
```
sudo systemctl enable /etc/systemd/system/node1.service
```
```
sudo systemctl start node1.service
```
```
sudo systemctl status node1.service
```
* Setzen des wol.timer
```
sudo nano /etc/systemd/system/node1.timer
```
```
[Unit]
Description="node1 starten"

[Timer]
OnCalendar=Mon-Fri *-*-* 07:30:00
Persistent=true
Unit=wol.service

[Install]
WantedBy=timers.target
```
```
sudo systemctl enable /etc/systemd/system/node1.timer
```
```
sudo systemctl start node1.timer
```
```
sudo systemctl status node1.timer
```
> src: Manuel
