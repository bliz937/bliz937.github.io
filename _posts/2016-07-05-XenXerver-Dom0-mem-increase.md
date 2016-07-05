---
layout: post
title:  "Increasing XenServer dom0 memory"
date:   2099-07-05 13:00:00 +0200
description: XenServer comes with the default dom0 memory at under a gb, I needed to increase that.
tags: xen, xenserver, citrix, virtual, virtual machine, vm, memory, ram
---
XenServer 6 has a **dom0** memory allocation of under 1gb.

## My problem
I want to increase that.

## My fix
For XenServer 6, I found that changing ```dom0_mem``` in

```/boot/extlinux.conf```,

 along with ```max``` that followed it, did the trick.

For XenServer 7, the config file seems to have changed to

```/boot/grub/grub.cfg```,

however, I did not test changing it here.
