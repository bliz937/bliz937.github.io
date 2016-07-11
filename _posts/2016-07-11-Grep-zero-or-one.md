---
layout: post
title:  "Grep ?"
date:   2016-07-11 13:00:00 +0200
description: "Grep ?, zero or one, not working."
tags: linux centos regex pattern matching grep egrep
---
Writing a script, I wanted to match two file types; **.xva** and **.xva.gz**.

## My problem
Running

```bash
ls | grep "xva(.gz)?"
```

did not work.

The above command in plain english reads:

 ```ls```: list all the files in the current directory.

```grep xva```: give me all files ending with ".xva".

```(.gz)?```: optionally, include the files that also have a ".gz" at the end.

## My fix
I remember seeing different variants of ```grep```, but never have I actually used them.
Regular ```grep``` does not cater for certain meta-characters, **?** being one of them.

Extended regular expression caters for **?**, ```grep -E``` or ```egrep```.

My command should thus look like

```bash
ls | grep -E "xva(.gz)?"
```

I discovered this on [Stackoverflow](https://unix.stackexchange.com/questions/10004/what-does-mean-in-a-regular-expression#answer-10005).
