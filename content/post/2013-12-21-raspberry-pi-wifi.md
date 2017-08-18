---
title: Raspberry Pi Wifi
author: Bill Farmer
type: post
date: 2013-12-21T20:16:37+00:00
url: /raspberry-pi-wifi/
categories:
  - Hacking

---
When I got my Pi some months ago I bought a wifi adapter for it, as you do. But I couldn&#8217;t get it to work reliably in Raspbian, and I didn&#8217;t even try in Arch Linux. So I tested the adapter on my windoze box, and it worked just fine, and I tried it with Linux and it worked just fine, so I put it in the &#8216;too hard&#8217; box.

I dug it out again the other day because I wanted to hear just how good the [Sonivox EAS library][1] I ported to the Pi sounded when hooked up to the hifi with some decent speakers. After extensive googling, and putting together various bits and pieces of info from various blog posts, and wikis, I came up with sort-of working configs for Raspbian and Arch Linux.

## Raspbian
```
    /etc/network/interfaces:
    
    auto lo
    iface lo inet loopback
    
    iface eth0 inet dhcp
    
    auto wlan0
    iface wlan0 inet dhcp
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

You can use `wpa_passphrase` to generate the `wpa_supplicant.conf` file:

    $ wpa_passphrase ssid passphrase
    network={
            ssid="ssid"
            #psk="passphrase"
            psk=41167ee7545cb4d9b1c7d7e113cfc1f0ac367b020141c46c9418965c1926fd80
    }
```
Redirect the output to the `wpa_supplicant.conf` file. I found this in the Arch Linux wiki. The wpa-conf and wpa-roam stanzas do not appear to be officially documented anywhere I can find.

### Raspbian Jessie

If your wifi router has a WPS button, press it. Run `wpa_cli` on the Pi and enter the following commands:
```
    > wps_pbc
    > quit
```
This should automatically pick up the ssid and key, connect, and set up `wpa_supplicant.conf` so that the Pi will reconnect when rebooted.

## Arch Linux

Use `wifi-menu` to create a `netctl` profile. Use `netctl list` to list profiles and ``When I got my Pi some months ago I bought a wifi adapter for it, as you do. But I couldn&#8217;t get it to work reliably in Raspbian, and I didn&#8217;t even try in Arch Linux. So I tested the adapter on my windoze box, and it worked just fine, and I tried it with Linux and it worked just fine, so I put it in the &#8216;too hard&#8217; box.

I dug it out again the other day because I wanted to hear just how good the [Sonivox EAS library][1] I ported to the Pi sounded when hooked up to the hifi with some decent speakers. After extensive googling, and putting together various bits and pieces of info from various blog posts, and wikis, I came up with sort-of working configs for Raspbian and Arch Linux.

## Raspbian
```
    /etc/network/interfaces:
    
    auto lo
    iface lo inet loopback
    
    iface eth0 inet dhcp
    
    auto wlan0
    iface wlan0 inet dhcp
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
```
You can use `wpa_passphrase` to generate the `wpa_supplicant.conf` file:
```
    $ wpa_passphrase ssid passphrase
    network={
            ssid="ssid"
            #psk="passphrase"
            psk=41167ee7545cb4d9b1c7d7e113cfc1f0ac367b020141c46c9418965c1926fd80
    }
```
Redirect the output to the `wpa_supplicant.conf` file. I found this in the Arch Linux wiki. The wpa-conf and wpa-roam stanzas do not appear to be officially documented anywhere I can find.

### Raspbian Jessie

If your wifi router has a WPS button, press it. Run `wpa_cli` on the Pi and enter the following commands:
```
    > wps_pbc
    > quit
```
This should automatically pick up the ssid and key, connect, and set up `wpa_supplicant.conf` so that the Pi will reconnect when rebooted.

## Arch Linux

Use `wifi-menu` to create a `netctl` profile. Use `netctl list` to list profiles and`` to enable and start your profile.
```
    sudo netctl enable wlan0-yourSSIDhere
    sudo netctl start wlan0-yourSSIDhere
```
This appears to work just fine.

 [1]: https://github.com/billthefarmer/mididriver
