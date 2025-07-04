---
draft: false
date: 2022-08-10T00:29:22+02:00
lastmod: 2025-07-01T15:33:00+02:00
title: "List of Free Software I Use"
description: ""
tags:
  - FOSS
  - GNU/Linux
categories:
  - software
series:
type: software
---
This is an (incomplete) list of the great free software I use.

> “Free software” means software that respects users' freedom and community.
> Roughly, it means that the users have the freedom to run, copy, distribute, study, change and improve the software.
> Thus, “free software” is a matter of liberty, not price.
> To understand the concept, you should think of “free” as in “free speech,” not as in “free beer”.
> We sometimes call it “libre software,” borrowing the French or Spanish word for “free” as in freedom, to show we do not mean the software is gratis.
>
> https://www.gnu.org/philosophy/free-sw.html

No proprietary garbage will be included in this list.

If you are interested in seeing what configuration options I use, check out
[my dotfiles repository](https://github.com/ondras12345/dotfiles).

## List
### Operating systems
- [Ubuntu](https://ubuntu.com/) GNU/Linux distribution
- [Lubuntu](https://lubuntu.me/) GNU/Linux distribution
- [LineageOS](https://lineageos.org/) Android distribution
- [Zephyr](https://www.zephyrproject.org/) RTOS

### Programming, file management
- [Git](https://git-scm.com/) distributed version control system
- [Vim](https://www.vim.org/) text editor
- [Vifm](https://vifm.info/) file manager with Vim-like key bindings
- [FileZilla](https://filezilla-project.org/) FTP client
- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) terminal emulator and serial console
- [Doxygen](https://doxygen.nl/) documentation generator

### Document creation and data processing
- [TeX Live](https://www.tug.org/texlive/) TeX distribution
- [gnuplot](http://www.gnuplot.info/) graphing utility
- [Jupyter Notebook](https://jupyter.org/)
- [GNU Octave](https://www.gnu.org/software/octave/index) scientific programming language
- [OpenModelica](https://openmodelica.org/) Modelica environment for modeling of dynamic systems
- [OMPython](https://github.com/OpenModelica/OMPython/) Python interface to OpenModelica
- [python-control](http://python-control.org/) Python Control Systems Library
- [JabRef](https://www.jabref.org/) citation / reference manager

### Note taking
- [I use todo.txt and Markdown files for my
  notes](/posts/2022-11-14-note-taking-system-todotxt-markdown/).
- Vim on PC
- [Markor](https://github.com/gsantner/markor) text editor for Android with
  special buttons for convenient editing of Markdown and todo.txt (among other
  formats)
- [Syncthing](https://syncthing.net/) continuous file synchronization program
- [todo.txt-vim](https://gitlab.com/dbeniamine/todo.txt-vim)
  (fork by dbeniamine)
- [todo.txt-cli](https://github.com/todotxt/todo.txt-cli)
  and [some add-ons](https://github.com/ondras12345/dotfiles/tree/linux/.config/todo/actions.d)
- [Radicale](https://radicale.org/v3.html) CalDAV and CardDAV server

### Media
- [Inkscape](https://inkscape.org/) vector graphics editor
- [GIMP](https://www.gimp.org/) image editor
- [Zathura](https://pwmt.org/projects/zathura/) document viewer with Vim-like key bindings
- [digiKam](https://www.digikam.org/) image organizer
- [Shotcut](https://shotcut.org/) video editor
- [Audacity](https://www.audacityteam.org/) audio editor
- [VLC](https://www.videolan.org/vlc/) media player
- [Spot](https://github.com/xou816/spot) native Spotify client for the GNOME desktop
- [Mopidy](https://mopidy.com/) extensible music server
- [Snapcast](https://github.com/badaix/snapcast) synchronous multiroom audio player

### Electronics engineering
- [KiCAD](https://kicad-pcb.org/) electronics design automation suite
- [CircuitJS1](https://www.falstad.com/circuit/) online circuit simulator
- [InvenTree](https://inventree.org/) inventory management system
- [inventree\_part\_import](https://github.com/30350n/inventree_part_import) CLI to import parts from suppliers like DigiKey, LCSC, Mouser, etc. to InvenTree 
- [inventree\_kicad](https://github.com/afkiwers/inventree_kicad) KiCad EDA conform API endpoint for InvenTree

### CNC
Common:
- [pcb2gcode](https://github.com/pcb2gcode/pcb2gcode) command-line tool for
  isolation, routing and drilling of PCBs
- [FlatCAM](http://flatcam.org/) PCB CAM - no longer actively used
- [FreeCAD](https://www.freecadweb.org/) 3D CAD modeler and CAM

[Old machine](/projects/1610-cnc/):
- [Grbl](https://github.com/gnea/grbl) CNC machine firmware
- [bCNC](https://github.com/vlachoudis/bCNC) Grbl controller
- [Candle](https://github.com/Denvi/Candle) Grbl controler - no longer
  actively used

New machine:
- [LinuxCNC](https://linuxcnc.org/) motion controller

### Home automation
- [Home Assistant](https://www.home-assistant.io/)
- [MQTT IO](https://github.com/flyte/mqtt-io) for exposing RPi GPIO via MQTT
- [Zigbee2MQTT](https://www.zigbee2mqtt.io/)
- [Traccar](https://www.traccar.org/)
- [AppDaemon](https://appdaemon.readthedocs.io/en/latest/)
{{< todo >}}
- TODO separate page for HA
{{< /todo >}}

### Others
- [Hugo](https://gohugo.io/) static webpage generator
- [Vimium](https://github.com/philc/vimium) web browser plugin for Vim-like key bindings
- [Termux](https://termux.com/) Android terminal emulator with standard Linux packages
- [pass](https://www.passwordstore.org/), the standard unix password manager
- ...
