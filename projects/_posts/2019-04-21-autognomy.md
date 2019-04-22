---
layout: post
title: Autognomy
date: 2018-04-21
desc: "Make your gnome desktop your mood ring: a command-line application written using Haskell's Turtle library, to change your wallpaper, look and theme with a single command."
category: project
cssfile: post.css
---

Autognomy is a command-line application that automatically sets the "aesthetic" or look for your Gnome desktop and utilities, as you want it.

Each "aesthetic"- your desktop look- should be defined as a JSON file, that currently contains the following fields:

* wallpaper 
* gtk-theme
* shell-theme
* icon-theme 

The application only works if you have the themes, icon themes et cetera already on your PC- the assumption is that you have a bunch of predefined "looks", defined in the `aesthetic` files, and the application allows you to change between looks at a single click.

## Usage

```bash
$ ./build.sh
$ ./autognomy <path-to-json-file>
``` 

## Development

* I'm currently working on getting sublime text, vim and emacs aesthetics up.
* I'm also trying to get it to auto-download themes, icon-themes etc. when these are not already installed in the system.
* Fuzzy-matching for theme and wallpaper names might be useful.

[Here's the github link for the project. Contributions are welcome!](https://github.com/AdLucem)