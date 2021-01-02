
TP-Link Wi-Fi Smart Plug HS100 & HS110 Monitor and Controller
============

## About

The [tp-link Wi-Fi Smart Plug model HS100](http://www.tp-link.us/products/details/HS100.html) and [tp-link Wi-Fi Smart Plug model HS110](https://www.kasasmart.com/us/products/smart-plugs/kasa-smart-plug-energy-monitoring-hs110) are an embedded Linux computer with a Wifi chip, a 110/220 V AC relay with a 15 A current limit, and a grounded electrical socket. You pair with it by establishing an ad-hoc network between the plug and a smartphone (also called Wi-Fi direct). After giving your router's SSID and access information, the plug connects to it and you can control the plug with the app provided by tp-link, called Kasa. One downside of using Kasa is that it's really not much more than a wall-switch in an app, though it does have pretty rich timer features which are nice. But you can't do things like turn the light on or off in response to events on the internet. Tp-link does provide a network control mode, but you have to pass control of your plug over to them, which isn't particularly great if you endeavor to remain the master of your own domain, haha only serious.

This repository is forked from [branning/hs100](https://github.com/branning/hs100)

## Added features

1. Support for injection of data into InfluxDB
2. Injection into InfluxDB via systemd timer and service
3. Visualise data with Grafana
4. Discover smart plugs over different network

## control the tp-link hs100 and hs110 WLAN smart plug

Script to connect over TCP/IP to an hs100/hs110 smart plug, switch it on and off, and query status information. You'll need the IP address and port (was 9999 in my tests) and a command, e.g.:

Print what IP your HS100 has:
```sh
hs100.sh discover
```

Discover IP, and save it for later:
* if you use sudo, then an entry is written into `/etc/hosts` for the HS100, if it is found
* if you don't do this, you must add an `-i 192.168.1.20` arguments to all other `hs100.sh` commands, e.g. `hs100.sh -i 192.168.1.20 on`
```
sudo hs100.sh discover
```
Or you can discover IP on a user-defined network range.
Example:
```
sudo hs100.sh -i "10.10.1.1-255" discover
```
Switch plug on:
```sh
hs100.sh on
```

Switch plug off:
```sh
hs100.sh on
```

Check if the plug is on or off:
```sh
hs100.sh check
```

Print plug system status:
```sh
hs100.sh status
```

Print power consumption (works only with hs110):
```sh
hs100.sh emeter
```
To inject emeter data into InfluxDB and visualize data in Grafana you have to install them first
1. Edit influxdb.config in hs100 directory and modify the configuration
```sh
vi influxdb.config
```
2. Inject into InfluxDB where IP or hostname from /etc/hosts is passed to -i parameter
```sh
hs100.sh -i hs100 emeter_influxdb
```
Check if data is injected into InfluxDB
Each smart plug should be each own measurement.

3. Import hs100-grafana.json to the Grafana via Web Gui

You can also add the script to the systemd so InfluxDB injection is executed every n time

1.  Edit systemd/hs100@.service file to fit your needs (path to script and user)
Example:
```sh
[Unit]
Description=HS100 Smart Plug by Tp-Link (instance %I)
[Service]
Type=oneshot
ExecStart=/root/hs100/hs100.sh -i %i emeter_influxdb
User=root
Group=systemd-journal
```
2. Edit systemd/hs100@.timer file to fit your needs (OnUnitActiveSec = execute script every n seconds).
Example:
```sh
[Unit]
Description=Periodic checking of %I status
[Timer]
OnUnitActiveSec=5s
AccuracySec=1
OnBootSec=10s
[Install]
WantedBy=hs100@%i.service
```
3. Add service and timer file to the systemd
```sh
cp systemd/* /etc/systemd/system/
systemctl daemon-reload
```
Enable and start the service for each smart plug. (Replace IP with your own IP or hostname from /etc/hosts)
```sh
systemctl enable hs100@192.168.1.50.timer
systemctl start hs100@192.168.1.50.timer
```
or
```sh
systemctl enable hs100@hs100.timer
systemctl start hs100@hs100.timer
```
This is the end result:
[![](https://github.com/gjumic/hs100/raw/master/screenshots/screenshot1.PNG)](https://github.com/gjumic/hs100/raw/master/screenshots/screenshot1.PNG)
[![](https://github.com/gjumic/hs100/raw/master/screenshots/screenshot2.PNG)](http://https://github.com/gjumic/hs100/raw/master/screenshots/screenshot2.PNG)

Note that I had only 1 smart plug so more testing is needed with multiple ones.
