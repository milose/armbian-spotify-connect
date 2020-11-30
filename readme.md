# Play Spotify Over Orange Pi Zero

Use this instead:
https://github.com/dtcooper/raspotify

https://urakozz.medium.com/airplay-receiver-with-orange-pi-51d6bdcbc46f
https://github.com/mikebrady/shairport-sync/blob/master/INSTALL.md

## Armbian

https://www.armbian.com/orange-pi-zero/
Debian: Buster

To configure:
```bash
sudo armbian-config
```

Configure to taste.

## Setting Up Defualt Output

```conf
# /etc/asound.conf 
pcm.!default {
    type hw
    card 0
    device 0
}

ctl.!default {
    type hw
    card 0
    device 0
}
```

## Installing 

### Prereqeusties

```bash
sudo apt-get install avahi-utils
```

Create service file:
```bash
sudo nano /etc/systemd/system/spotify-avahi.service
```

```ini
[Unit]
Description=Spotify Connect Zeroconf
After=network.target

[Service]
User=root
ExecStart=/usr/bin/avahi-publish-service orange _spotify-connect._tcp 4000 VERSION=1.0 CPath=/login/_zeroconf
Restart=always
RestartSec=10
StartLimitInterval=30
StartLimitBurst=20

[Install]
WantedBy=multi-user.target
```

Enable autostart:
```bash
systemctl enable spotify-avahi.service
```

### Spotify Connect Web

```bash
sudo su
mkdir /services
cd /services
wget https://github.com/Fornoth/spotify-connect-web/releases/download/0.0.4-alpha/spotify-connect-web_0.0.4-alpha.tar.gz
tar zxvf spotify-connect-web_0.0.4-alpha.tar.gz
cd spotify-connect-web
#copy the key to this directory
```

Create service file:
```bash
sudo nano /etc/systemd/system/spotify-connect.service
```

```ini
[Unit]
Description=Spotify Connect
After=network.target

[Service]
User=root
WorkingDirectory=/services/spotify-connect-web/
ExecStart=/services/spotify-connect-web/spotify-connect-web --bitrate 320 --name <streamer_name> --username <username> --password <password> -o hw:0,0
Restart=always
RestartSec=10
StartLimitInterval=30
StartLimitBurst=20

[Install]
WantedBy=multi-user.target
```

Enable autostart:
```bash
systemctl enable spotify-connect.service
```

## Notable Commands

```bash
# Alsa Mixer
alsamixer
alsactl store

# List Audio Devices
aplay -l
aplay -Ls
```