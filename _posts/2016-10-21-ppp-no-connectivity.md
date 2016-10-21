---
layout: post
title:  "No connectivity on ppp"
date:   2016-10-21 13:00:00 +0200
description: "Setting up a PPPOE connection with ppp, there is no internet connectivity."
tags: arch linux archlinux network pppoe ppp pppd
---

On Arch Linux, I want to setup a [PPPOE](https://en.wikipedia.org/wiki/PPPoE) connection. In other Linux distributions, I could find a GUI setup under the network settings, however, it was not the case here. An [archlinux wiki](https://wiki.archlinux.org/index.php/pppd) post details how to achieve a connection using the [ppp](https://www.archlinux.org/packages/?name=ppp) package. I followed the steps and managed to connect, by running ```journalctl -b --no-pager | grep pppd```, I got:

```bash
Oct 21 13:10:28 jupiterx pppd[28779]: Starting link
Oct 21 13:10:28 jupiterx pppd[28779]: PPP session is 3
Oct 21 13:10:28 jupiterx pppd[28779]: Connected to 9c:97:26:zz:yy:xx via interface wlann0
Oct 21 13:10:28 jupiterx pppd[28779]: Connect: ppp0 <--> wlann0
Oct 21 13:10:28 jupiterx pppd[28779]: Remote message: Login ok
Oct 21 13:10:28 jupiterx pppd[28779]: PAP authentication succeeded
Oct 21 13:10:28 jupiterx pppd[28779]: peer from calling number 9C:97:26:zz:xx:yy authorized
Oct 21 13:10:28 jupiterx pppd[28779]: Local IP address changed to 169.1.150.91
Oct 21 13:10:28 jupiterx pppd[28779]: not replacing existing default route via 192.168.1.1
Oct 21 13:10:28 jupiterx pppd[28779]: Cannot determine ethernet address for proxy ARP
```

## My problem

Although the log output shows that I am connected, my connection was still not being routed via the PPPOE connection.

## My fix

Not that I saw it at the time, but the log above reveals the problem: ```not replacing existing default route```.

I looked at ```route```:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         myrouter.Home   0.0.0.0         UG    600    0        0 wlann0
41.170.a.b    0.0.0.0         255.255.255.255 UH    0      0        0 ppp0
192.168.1.0     0.0.0.0         255.255.255.0   U     600    0        0 wlann0
```

and noticed that my router is the default gateway.

Looking at ```man route```, I then ran

```bash
sudo route del default
```

to delete ```myrouter.Home``` and then

```bash
sudo route add default gw 41.170.a.b
```

where ```41.170.a.b``` is the IP of the ```ppp0``` interface - the interface used for the PPPOE connection.

Running ```route``` again shows us that the ```ppp0``` interface is now the default gateway:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         gateway         0.0.0.0         UG    0      0        0 ppp0
gateway         0.0.0.0         255.255.255.255 UH    0      0        0 ppp0
192.168.1.0     0.0.0.0         255.255.255.0   U     600    0        0 wlann0
```
