# WOL
Automatische WOL Schaltung eines Servers mithilfe von systemd.
Client sendet WOL Signal an Server. Server fährt automatisch über Nacht herunter.


## Prämisse:
Client und Server müssen sich im selben Subnetz befinden.
WOL fähiges Mainboard im Server.


## Automatisches Herunterfahren über systemd:

* Option 1: Automatisches Herunterfahren
```
nano /etc/systemd/system/shutdown.service
```
```
[Unit]
Description=Nachtabschaltung dauerhaft

[Service]
WorkingDirectory=/root/
Type=oneshot
ExecStart=/bin/bash shutdown.sh

[Install]
WantedBy=basic.target
```


```
nano shutdown.sh
```
```
shutdown 23:30 "Automatisches Herunterfahren"
```

```
systemctl enable /etc/systemd/system/shutdown.service
```
```
systemctl status shutdown.service
```

* Option 2: Automatisches Herunterfahren mit Timer:
```
nano /etc/systemd/system/shutdown.service
```
```
[Unit]
Description="Automatisches Herunterfahren"

[Service]
WorkingDirectory=/root/
Type=oneshot
ExecStart=/bin/bash shutdown.sh
KillMode=process

[Install]
WantedBy=multi-user.target 
```
```
nano /etc/systemd/system/shutdown.timer 
```
```
[Unit]
Description="Nachabschaltung Timer"

[Timer]
OnCalendar=*-*-* 18:00:00
Persistent=true
Unit=shutdown.service

[Install]
WantedBy=timers.target
```
```
nano shutdown.sh
```


```
shutdown 23:30 "Automatisches Herunterfahren"
```

```
systemctl enable /etc/systemd/system/shutdown.service
```
```
systemctl enable /etc/systemd/system/shutdown.timer
```
```
systemctl status shutdown.service
```
```
systemctl status shutdown.timer
```
## Vorbereitung des Clients:
* Installieren von ethtool
```
apt update && sudo apt upgrade -y
```
```
apt install ethtool
```
```
nano wol.sh
```
```
wakeonlan MAC ADRESS
```
* Setzen des wol.service
```
nano /etc/systemd/system/wol.service
```
```
[Unit]
Description=node2 starten

[Service]
WorkingDirectory=/root/
Type=oneshot
ExecStart=/bin/bash wol.sh
KillMode=process

[Install]
WantedBy=multi-user.target
```
```
systemd enable /etc/systemd/system/wol.service
```
```
systemd status wol.service
```
* Setzen des wol.timer
```
nano /etc/systemd/system/wol.timer
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
systemd enable /etc/systemd/system/wol.timer
```
```
systemd status wol.timer
```
> src: IT Solution Linder
