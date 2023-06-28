---
draft: false
date: 2022-11-14T16:40:01+01:00
title: "My note taking system with todo.txt and Markdown"
description: >-
  I have long searched for a good free software replacement for Google Keep,
  and todo.txt might just be the perfect solution.
featured_image: "featured.png"
tags:
  - FOSS
  - GNU/Linux
categories:
  - software
series:
---
{{< figure src="featured.png" width="100%" >}}

I have recently changed the way I sync notes between by devices.
This post will document the process of setting up the new system,
as well as the reasons why I settled on this solution. It might occasionally
get updated with new information I find worth noting.

For a long time, I used **Google Keep** to sync my To-Do list across multiple
devices. However, this had several disadvantages:
- Non-free software.
- Google can read everything.
- Cannot be easily edited with a real text editor (Vim, obviously).
- Hard to back up.
- When check boxes are enabled, the only way to reorder lines is with the
  mouse. When check boxes are disabled, indentation / grouping is lost and
  undo does not work (and there's no backup...).

The new system is much better. It uses actual text files in my filesystem,
so my notes can be easily edited and processed with all sorts of programs.

One of my main requirements was that I need to be able to add items to my
To-Do list both on my Android phone and on my PCs. Synchronization should
happen automatically whenever my devices are online.

## First attempts -- GitJournal
At first, [GitJournal](https://github.com/GitJournal/GitJournal) looked very
promising. It uses Markdown to create notes and Git for synchronization over
SSH. However, I have quickly discovered that it couldn't properly handle sync
conflicts. This problem might get resolved in the future, so I'll leave a link
to the Github issue: https://github.com/GitJournal/GitJournal/issues/241
Until then, it is not suitable for me.


## Current solution
### Vim
Just about the only thing I needed to
[add in my `.vimrc`](https://github.com/ondras12345/dotfiles/blob/6701073d74f447b334a6473e24360192881073b3/.vimrc#L69)
was the [todo.txt-vim](https://gitlab.com/dbeniamine/todo.txt-vim)
plugin (fork by dbeniamine). This provides mappings and syntax highlighting
for `todo.txt` files.

### Markor
When searching for free and open-source Android note taking apps, I came
across [Markor](https://github.com/gsantner/markor). It's a pretty good text
editor with special functions (extra buttons, syntax highlighting, etc.) for
working with formatted files -- Markdown, [todo.txt](http://todotxt.org/)
and more.

The only thing Markor cannot do is syncing notes between devices.
However, that is actually an advantage. It does one thing pretty well (Unix
philosophy). It works with text files in the filesystem that can be synced by
another piece of software.

### Git
Initially I had the idea of using Git for synchronization. It is a version
control system, so it works really well with text files. It can do automatic
3-way merge, `diff`ing revisions, etc. However, I **couldn't find any suitable
Git client app for Android**. Using command-line `git` in Termux was not an
option because I would either have to run the sync command manually or keep
a program running in Termux at all times, which would probably significantly
shorten battery life.

### Syncthing
[Syncthing](https://syncthing.net/) is an open-source continuous file
synchronization program. There is an
[official Android client](https://f-droid.org/packages/com.nutomic.syncthingandroid/),
as well as client programs for all major desktop operating systems.

For this use case, there is not much data to sync, so it is not necessary to
set up port forwarding to make Syncthing reachable from the Internet. The
[relay servers](https://docs.syncthing.net/users/relaying.html) (no
configuration necessary) should be sufficient for the situations where my
devices are not on the same LAN.

To be able to sync data between two devices, both of them have to be online
simultaneously. Syncthing only syncs data on your drives, there is no cloud
storage. To make sure I never find myself in a situation where some
notes are only available on a powered off PC because all other devices were
offline at the time they were created, I deployed Syncthing on my server (a
Raspberry PI 4 model B):
```sh
# follow Syncthing installation instructions
# ...

sudo systemctl enable --now syncthing@$USER.service
sudo systemctl status syncthing@$USER.service

sudo ufw allow syncthing
```

To access the web GUI from another computer without having to configure
Syncthing to listen on `0.0.0.0` instead of the default `127.0.0.1`:
```sh
ssh -L 8385:localhost:8384 server
# You can now connect to http://localhost:8385/ with your web browser
```


### Advantages of the new system
- Everything is a text file. Easy to search through and process
  programmatically, future-proof.
- Can be edited with Vim.
- Easy to back up -- I just added the notes directory to my `duplicity` backup
  script.
- Much better To-Do list format --
  see [todo.txt format specification](https://github.com/todotxt/todo.txt).
  It can do due dates, priorities, projects, ...


### Miscellaneous notes
#### `.stignore` for Vim temporary files
- https://forum.syncthing.net/t/useful-stignore-patterns/1175

Put this in `.stignore`:
```text
// Firefox downloads and other things
*.part
// Chromium downloads
*.crdownload

// Temporary / Backup Files
*~
.*.swp
```


#### Conflict notifications
From time to time, a conflict is encountered. This happens when two devices
change a single file at the same time. To notify myself when this happens,
I added this to my `crontab` on my server:
```sh
7 * * * * conflicts=$(find ~/Documents/notes/ -name '*.sync-conflict-*') ; [ -n "$conflicts" ] && echo "$conflicts" | mail -s "[Syncthing] conflict" ondra
```
`ondra` is my `$USER`.
I have configured `exim4` to forward local mail to my email address.


#### Version control
Syncthing can be configured to store old versions of files in a directory
named `.stversions`. Obviously, this is not nearly as good as Git.
However, as of 2022-11 I use no version control other that this (and my daily
backups).


#### Recurrent tasks
While `todo.txt-vim` handles
recurrent tasks in `todo.txt` (e.g. `rec:+1w`), Markor does not.
This is their reasoning: https://github.com/gsantner/markor/issues/1263
For now, I'll stick with editing due dates manually on Android.


#### Priorities
Currently I use these priorities in my `todo.txt`:
- `(A)`--`(F)` normal priorities
- `(P)` project - used to tag steps belonging to a +project
- `(W)` waiting - tasks that are blocked by something
- `(Z)` someday - stuff that does not need to get done in the near future
