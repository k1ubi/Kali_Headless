<div align="center">

```
 _   __  ___   _     _____   _   _  _____  ___ ______ _      _____ _____ _____ 
| | / / / _ \ | |   |_   _| | | | ||  ___|/ _ \|  _  \ |    |  ___/  ___/  ___|
| |/ / / /_\ \| |     | |   | |_| || |__ / /_\ \ | | | |    | |__ \ `--.\ `--. 
|    \ |  _  || |     | |   |  _  ||  __||  _  | | | | |    |  __| `--. \`--. \
| |\  \| | | || |_____| |_  | | | || |___| | | | |/ /| |____| |___/\__/ /\__/ /
\_| \_/\_| |_/\_____/\___/  \_| |_/\____/\_| |_/___/ \_____/\____/\____/\____/
```

`[ no monitor // no keyboard // wifi up before first boot ]`

![kali](https://img.shields.io/badge/KALI_LINUX-ff00c8?style=for-the-badge&logo=kalilinux&logoColor=00fff9&labelColor=0a0014)
![pi](https://img.shields.io/badge/RASPBERRY_PI-00fff9?style=for-the-badge&logo=raspberrypi&logoColor=0a0014&labelColor=0a0014)

</div>

<br>

```
▓▒░ 0x00 // CURRENT METHOD ░▒▓
```

Get the Pi on your network before it ever boots — no display, no ethernet cable required.

```console
root@host:~# cp wpa_supplicant.conf /path/to/BOOT/wpa_supplicant.conf
root@host:~# nano /path/to/BOOT/wpa_supplicant.conf   # swap in your real ssid/psk
root@host:~# sync && eject /path/to/BOOT
```

`wpa_supplicant.conf` shipped in this repo:

```ini
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
country=IT
update_config=1

network={
 ssid="xxx"
 psk="xxx"
}
```

Drop it into the **BOOT** partition of the SD card, power the Pi on, and it associates before
you ever plug in a monitor.

<br>

```
▓▒░ 0x01 // OLD WAY (GUI-based, kept for reference) ░▒▓
```

Predates the `wpa_supplicant.conf` approach — connects via `nmcli` from inside a full desktop
session instead of at boot from the SD card.

```console
root@kali:~# sudo nano /etc/lightdm/lightdm.conf
```
```ini
[Seat *:*]
autologin-user=kali
autologin-user-timeout=0
```

```console
root@kali:~# sudo touch /etc/systemd/system/foo-daemon.service
root@kali:~# sudo nano /etc/systemd/system/foo-daemon.service
```
```ini
[Unit]
Description=Auto connect wifi
After=multi-user.target

[Service]
ExecStart=/usr/bin/nmcli connection up *SSID*

[Install]
WantedBy=multi-user.target
```

```console
root@kali:~# sudo chmod 664 /etc/systemd/system/foo-daemon.service
root@kali:~# sudo systemctl daemon-reload
root@kali:~# sudo systemctl enable /etc/systemd/system/foo-daemon.service
```

Also uncomment in `/boot/config.txt` to force HDMI detection at boot:

```diff
- #hdmi_force_hotplug=1
+ hdmi_force_hotplug=1
```

<br>

```
▓▒░ 0x02 // REMOTE DESKTOP (x11vnc) ░▒▓
```

```console
root@kali:~# apt-get update && apt-get install -y x11vnc
```

`x11vnc` gives the real desktop session (not a virtual one). If the framebuffer is too small,
edit `/boot/config.txt` (pull the SD card into another machine if `/boot` isn't mounted):

```diff
- #framebuffer_width=1280
- #framebuffer_height=720
+ framebuffer_width=1280
+ framebuffer_height=720
```

Reboot, then start `x11vnc`.

<br>

<div align="center">

`.: . . : <[ headless != blind ]> : . :.`

</div>
