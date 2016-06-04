---
layout: post
title:  "Problem moving data to another disk"
date:   2016-06-02 21:00:00 +0200
description: clonezilla partclone failing and grub saying unknown filesystem
tags: disk, clone, grub, boot
---
I got a new computer with the same size hard drive as my previous machine. Problem was, I had to use the new disk in the new machine.
I thought I'd use [CloneZilla](http://clonezilla.org/) to transfer the data from my old drive to my new drive.


## Some errors along the way
CloneZilla complained about the target drive not being propery unmounted.
As a result, I think, partclone failed. 

After the clone, and a reboot, grub will complain that **"Error: Unknown filesystem"**.
Some Googling left me with posts like [this](https://askubuntu.com/questions/142300/how-to-fix-error-unknown-filesystem-grub-rescue), and a few others.
None of which worked for me. 

## My fix
Since both drives are exactly the same size, I thought I would do a manual [dd](https://en.wikipedia.org/wiki/Dd_(Unix)).
Disk 1 was the target, and disk 2 was the source. My command looked like this

```bash
dd if=/dev/sdb of=/dev/sda status=progress
```

After a painful couple of hours, as apposed to just under 2 for CloneZilla, the copy was complete and my new disk was working like my old.

TL;DR: CloneZilla was failing me, did manual dd on two drives with the same size.
