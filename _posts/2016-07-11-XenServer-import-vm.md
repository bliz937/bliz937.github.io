---
layout: post
title:  "XenServer&#58 import VM"
date:   2016-07-11 13:00:00 +0200
description: "Importing a bunch of VMs (xva) to XenServer"
tags: linux centos XenServer Virtual VIrtualMachine import
---
I have a bunch of saved VMs as ```.xva``` in a directory and want to import them.

## My problem
Citrix provides the ```xe``` tool which allows you to import snapshots using

```bash
xe vm-import filename=./vm-snapshot.xva
```


Problem is, I have a few VMs to import and do not want to do it manually.

## My fix
I wrote a simple script that imports all the VMs in a directory.

The script can be found in the gist below. It worked for me, but I take no responsibility if it does not work for you, so use at your own risk. :)

{% gist fcdffc9e1b7a3613872912ce55ce6716 %}
