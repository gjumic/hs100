
TP-Link Wi-Fi Smart Plug HS100 & HS110 Monitor and Controller
============

## About

The [tp-link Wi-Fi Smart Plug model HS100](http://www.tp-link.us/products/details/HS100.html) [tp-link Wi-Fi Smart Plug model HS110](https://www.kasasmart.com/us/products/smart-plugs/kasa-smart-plug-energy-monitoring-hs110) are an embedded Linux computer with a Wifi chip, a 110/220 V AC relay with a 15 A current limit, and a grounded electrical socket. You pair with it by establishing an ad-hoc network between the plug and a smartphone (also called Wifi direct). After giving your router's SSID and access information, the plug connects to it and you can control the plug with the app provided by tp-link, called Kasa. One downside of using Kasa is that it's really not much more than a wall-switch in an app, though it does have pretty rich timer features which are nice. But you can't do things like turn the light on or off in response to events on the internet. Tp-link does provide a network control mode, but you have to pass control of your plug over to them, which isn't particularly great if you endeavor to remain the master of your own domain, haha only serious.

This repository is forked from [branning/hs100](https://github.com/branning/hs100)

## Added features

1. Support for injection of data into InfluxDB
2. Injection into InfluxDB via systemd timer and service
3. Visualise data with Grafana
4. Discover smart plugs over different network

## control the tp-link hs100 and hs110 wlan smart plug

Script to connect over TCP/IP to an hs100/hs110 smart plug, switch it on and off and query status information. You'll need the IP address and port (was 9999 in my tests) and a command, e.g.:

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
Or you can discover IP on user defined network range.
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

Check if plug is on or off:
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
