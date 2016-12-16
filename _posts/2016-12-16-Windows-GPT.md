---
layout: post
title:  "Windows unable to read Linux formatted GPT drive"
date:   2016-12-16 12:00:00 +0200
description: "Windows fail to access a GPT drive that was originally residing in a Linux machine."
tags: linux windows disk GPT MBR harddisk harddrive
---

There was a >2TB drive being used in a Linux machine.
The drive was formatted with the [NTFS](https://en.wikipedia.org/wiki/NTFS) file system, with a  [GPT](https://en.wikipedia.org/wiki/GUID_Partition_Table) partition table.
It was used for some time and accumulated data on it.

## My problem

We needed to migrate this drive to a Windows machine, and since the file system was NTFS, it should have not been a problem.
However, we found that the drive did not appear in 'My Computer' and only showed in [disk manager](http://www.howtogeek.com/101862/how-to-manage-partitions-on-windows-without-downloading-any-other-software/), without any drive letters associated with it.

How do we get the drive to appear in My Computer?

## My fix

Although various attempts were made to fix the above problem, a rather simple use of [TestDisk](http://www.cgsecurity.org/wiki/TestDisk) did the trick.
We ran TestDisk on the drive, as Windows could detect it as seen in disk manager, and used the **Analyze** option.

It showed that the filesystem was unknown, but after the analyze command finished, it fixed the problem preventing Windows from properly accessing the drive.
