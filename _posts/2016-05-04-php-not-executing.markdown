---
layout: post
title:  "php not executing"
date:   2016-05-04 21:00:00 +0200
tags: web server, php, apache
---
A third year student messaged me on Telegram, asking for assistance with a php error he was experiencing.

Simply put, all his php files were not being executed, and instead were being displayed.

I walked through checking the **php and apache error logs**; which was fruitless.

The problem then occured to me that he was viewing the php file from *disk* (with the full path in the url), instead of via apache itself.

To solve it, he had to view it as ```localhost/file.php```.


TL;DR: Was not viewing through web server but full path on disk.
