+++ 
draft = false
date = 2021-02-07T22:16:00+02:00
title = "Rooting my PocketBook Ultra 650"
description = ""
slug = ""
authors = []
tags = ["electronics", "linux"]
categories = ["software"]
externalLink = ""
series = []
+++

{{< figure src="pocketbook-screenshot.png" >}}

I wanted to root my PocketBook Ultra 650 e-book reader (firmware version
`5.14.788`).
It turns out that this is super simple to do. Just take it apart, hook up some
wires to the GND, RX and TX pins on the on-board UART connection and use a
3.3V FTDI breakout board to interface with it. Open the serial port at 115200
baud and turn the device on. You should see the kernel booting up.

{{< figure-resize src="PocketBook-UART.jpg" caption="Partially disassembled PocketBook Ultra 650. The UART connection is in the bottom right corner" >}}

Want a root shell? Just press Enter twice and you'll be greeted with `~ # `.

I wanted to run `sshd` on the device, so that I can connect to it without
needing to disassemble it.

```console
~ # mount -o rw,remount /
~ # mkdir /.ssh
~ # vi /.ssh/authorized_keys  # add your ssh key
~ # chmod 700 ~/.ssh
~ # chmod 600 ~/.ssh/authorized_keys
```

In addition to the steps listed above, I also modified the following files:

- In `/etc/sudoers` I added this:
  ```
  reader ALL=(ALL) NOPASSWD: /sbin/dropbear -s
  sreader ALL=(ALL) NOPASSWD: /sbin/dropbear -s
  ```
  I was not sure which one of the users runs the app and I did not bother
  trying to figure it out.

- Then I created `/mnt/ext1/applications/sshd.app`:
  ```sh
  #!/bin/sh
  /ebrmain/bin/sudo /sbin/dropbear -s
  ```

After doing this, a new app called `@Sshd` should appear in the apps
menu. When clicked, it starts the ssh server and you should be able to log in
as `root` with your `RSA` key. `ed-25519` will not work!
