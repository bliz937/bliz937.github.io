---
layout: post
title:  "DevExpress could not resolve reference"
date:   2016-07-04 13:00:00 +0200
description: Opening an old project, Visual Studio gave a warning about not finding the DevExpress reference.
tags: windows, visual studio, devexpress, code, c#
---
On a new machine, opening up an old project which used **DevExpress** controls, Visual Studio gave several warnings that the reference could not be resolved.

## My problem
The project had DevExpress version ```14.1.8``` whilst the installed controls are ```14.1.10```.

## My fix
Bundled in the DevExpress installer is the **DevExpress Components Project Converter**.
Simply running the tool and selecting the project directory, and clicking upgrade, upgrades the project to the installed control version. Going back to Visual Studio, reload the project and compile.
