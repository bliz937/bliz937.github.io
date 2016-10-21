---
layout: post
title:  "Changing NIC name"
date:   2016-10-12 13:00:00 +0200
description: "Changing Antergos/Arch Linux NIC name back to eth0/wlan0"
tags: arch linux archlinux nic network name
---
Using Antergos, I noticed that the NIC names on my laptop are ```wlp8s0``` instead of ```wlan0``` and ```enp9s0f0``` instead of ```eth0```.

## My problem
I wanted the old names.

## My fix
Arch's [wiki](https://wiki.archlinux.org/index.php?title=Network_configuration&redirect=no#Change_device_name) is at the rescue!

Running ```ip link```, we have an output of

```bash
[root@jupiterx shev]# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp9s0f0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 00:90:f5:b6:ab:9b brd ff:ff:ff:ff:ff:ff
3: wlp8s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DORMANT group default qlen 1000
    link/ether bc:77:37:01:ab:46 brd ff:ff:ff:ff:ff:ff
```

What we need from here is the value of ```link/ether```, which is the physical address of the networking interface.
Create a new file ```/etc/udev/rules.d/10-network.rules``` and add the following

```bash
vim /etc/udev/rules.d/10-network.rules
```

```bash
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="00:90:f5:b6:ab:9b", NAME="ethh0"
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="bc:77:37:01:ab:46", NAME="wlann0"
```

Where the first ```ATTR{address}``` value is that of ```enp9s0f0``` and the second being ```wlp8s0```.
The reason why we cannot use ```wlan0``` and ```eth0``` is to avoid a [race condition](https://en.wikipedia.org/wiki/Race_condition) between the kernel and udev during boot - as mentioned in the wiki.

A reboot and the changes should be applied.

```bash
[root@jupiterx shev]# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: ethh0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 00:90:f5:b6:ab:9b brd ff:ff:ff:ff:ff:ff
3: wlann0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DORMANT group default qlen 1000
    link/ether bc:77:37:01:ab:46 brd ff:ff:ff:ff:ff:ff
```
