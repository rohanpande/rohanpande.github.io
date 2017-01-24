---
layout: post
title:  "CONFIGURE DWA131 WIRELESS N300 NANO USB ADAPTER ON RASPBIAN JESSIE"
description: Today I have to shift network connection of my raspberry pi 2 from wired to wifi. I did foresee this and did purchased a wifi dongle “DWA131 Wireless N300 Nano USB Adapter” 
date:   2017-01-23 10:51:47 +0530
categories: post
img: DLINKDW131.jpg
categories: [one, two]
color: 1976D2
author: Rohan Pande
---

Today I have to shift network connection of my raspberry pi 2 from wired to wifi. I did foresee this and did purchased a wifi dongle “DWA131 Wireless N300 Nano USB Adapter” from a technological fair at my home town. When I connected this dongle to my raspberry pi. It was disappointing to see that this dongle is not supported out of the box for raspbian jessie. so I tried it with “Kodi” which to my surprise supported this dongle out of the box.

So during this weekend I decided I should fix this issue. I googled for solutions and found & tried many but none of them worked for me. then I looked up in detail and found out that most of the solutions were for DWA131 Wireless N150 Nano USB Adapter.

So this is what I did to fix my issue.

First I got the kernal and build number of “Raspbian Jessie” by doing

uname -a

output:

Linux rohan-pi 4.1.13-v7 + #826 SMP PREEMPT Fri Nov 13 20:19:03 GMT 2015 armv7l GNU/Linux

Kernal : 4.1.13-v7

Build : 826

Getting the right driver for dongle

wget https://dl.dropboxusercontent.com/u/80256631/8192eu -[ kernel ]-[ build ].tar.gz
so my wget would look like this

wget https://dl.dropboxusercontent.com/u/80256631/8192eu-4.1.13-v7-826.tar.gz

extracting downloaded driver

tar xzf 8192eu-4.1.13-v7-826.tar.gz
The last step is to run install.sh from the extracted folder.

please setup your wifi network configuration & remove the wired connection and reboot the system.

this is my wifi config setup

interface setting

path: /etc/network/interfaces

# interfaces(5) file used by ifup(8) and ifdown(8)

# Please note that this file is written to be used with dhcpcd
# For static IP, consult /etc/dhcpcd.conf and ‘man dhcpcd.conf’

# Include files from /etc/network/interfaces.d:
source-directory /etc/network/interfaces.d

auto lo
iface lo inet loopback
iface eth0 inet manual
auto wlan0
allow-hotplug wlan0
iface wlan0 inet manual
wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
allow-hotplug wlan1
iface wlan1 inet manual
wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
wpa_supplicant.conf file contents :

/etc/wpa_supplicant/wpa_suppplicant.conf
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
ssid=”YOUR-NETWORK-SSID”
key_mgmt=WPA-PSK
psk=”YOUR-NETWORK-PASSWORD”
}
