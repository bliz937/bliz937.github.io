---
layout: post
title:  "Telegram bot RuntimeError"
date:   2016-09-17 13:00:00 +0200
description: "Resolving the issue of 'RuntimeError: Conflict: terminated by other long poll or webhook'"
tags: server telegram bot python runtimeerror poll
---
Using the [Atom](https://atom.io/) editor to code a Python Telegram bot, with the [script](https://atom.io/packages/script) plugin by [rgbkrk](https://github.com/rgbkrk) to run code in Atom, I received an error when attempting to run the bot a second time.

## My problem
Using the keyboard shortcut ```ctrl-shitft-b``` to run the bot, initially the bot ran. I would then hit ```esc``` to stop the bot, but actually it just closed the script view window, leaving the bot running in the background.

When running the bot a second time, I received:

```
File "/usr/lib/python3.5/site-packages/aiotg/bot.py", line 221, in api_call
  raise RuntimeError(err_msg)
RuntimeError: Conflict: terminated by other long poll or webhook
```

## My fix
In terminal, I ran:

```bash
ps aux | grep -v grep | grep main.py
```

to see if there was an instance of the bot running - main.py is the bot's entry point.

Indeed the bot was running!

```
shev     27918  0.2  0.1 261484 27956 tty2     Sl+  11:33   0:00 /usr/bin/python -u /home/shev/bot/main.py
```

Simply running

```bash
kill 27918
```

stopped the bot.

However, looking at the script plugin documentation, my error was that I used ```esc``` instead of ```ctrl-q``` to kill the script process.

TL;DR: There is an existing instance of the bot running, kill it.
