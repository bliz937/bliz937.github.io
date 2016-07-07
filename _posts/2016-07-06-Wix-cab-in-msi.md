---
layout: post
title:  "Wix&#58 embed cab in installer"
date:   2016-07-06 13:00:00 +0200
description: "Embedding the cab files in a Wix project into the actual installer."
tags: wix, installer, cab, install, windows, wax, visual studio
---
I have a project which I wanted to create an installer for. [Wix](http://wixtoolset.org/) is a great tool but has a steep learning curve. I used [Wax](https://github.com/tom-englert/Wax), a Visual Studio plugin, to help simply the process a bit.

## My problem
When I built the Wix project, I received the installer alongside the [cab](https://support.microsoft.com/en-us/kb/310435) file. I wanted the cab file embedded in the installer.

## My fix
[Stackoverflow](https://stackoverflow.com/questions/11284625/wix-single-msi-instead-of-msi-cab) provided with a solution. The solution was to add ```EmbedCab="yes"``` to the **Media** element.

I did not have a **Media** element, I did however have an empty **MediaTemplate** element. Adding ```EmbedCab="yes"``` to it did the trick.
