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
systemctl start /etc/systemd/system/nachtschaltung.service
```
```
systemctl status nachtschaltung.service
```

* Option 2: Automatisches Herunterfahren mit Timer:
```
nano /etc/systemd/system/nachtschaltung.service
```
```
[Unit]
Description="Automatisches Herunterfahren"

[Service]
WorkingDirectory=/root/
Type=oneshot
ExecStart=/bin/bash nachtschaltung.sh
KillMode=process

[Install]
WantedBy=multi-user.target 
```
```
nano /etc/systemd/system/nachtschaltung.timer 
```
```
[Unit]
Description="Nachabschaltung Timer"

[Timer]
OnCalendar=*-*-* 18:00:00
Persistent=true
Unit=nachtschaltung.service

[Install]
WantedBy=timers.target
```
```
nano nachtschaltung.sh
```


```
shutdown 23:30 "Automatisches Herunterfahren"
```


```
systemctl enable /etc/systemd/system/nachtschaltung.timer
```
```
systemctl status nachtschaltung.service
```
```
systemctl status nachtschaltung.timer
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
sudo nano wol.sh
```
```
wakeonlan MAC ADRESS
```
* Setzen des wol.service
```
sudo nano /etc/systemd/system/wol.service
```
```
[Unit]
Description=Server starten

[Service]
WorkingDirectory=/root/
Type=oneshot
ExecStart=/bin/bash wol.sh
KillMode=process

[Install]
WantedBy=multi-user.target
```
```
sudo systemctl enable /etc/systemd/system/wol.service
```
```
sudo systemctl status wol.service
```
* Setzen des wol.timer
```
sudo nano /etc/systemd/system/wol.timer
```
```
[Unit]
Description="Server starten"

[Timer]
OnCalendar=Mon-Fri *-*-* 07:30:00
Persistent=true
Unit=wol.service

[Install]
WantedBy=timers.target
```
```
sudo systemctl enable /etc/systemd/system/wol.timer
```
```
sudo systemctl status wol.timer
```
> src: .
