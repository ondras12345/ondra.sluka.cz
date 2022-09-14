+++ 
draft = false
date = 2021-01-15T16:12:00+02:00
title = "Installing LOGO! Soft Comfort on Windows XP"
description = ""
slug = ""
authors = []
tags = ["PLC", "Windows XP"]
categories = ["software"]
externalLink = ""
series = []
+++
{{< figure src="LOGO-Soft-Comfort.png" caption="LOGO! Soft Comfort running on Windows XP" >}}

As you might already know, I run GNU/Linux on my main production machine. For
the occasions when I absolutely need to run a piece of Windows-only software,
I have set up a Windows XP virtual machine (VM) in VirtualBox.

My school required me to install LOGO! Soft Comfort, software used to
program Siemens LOGO!
[PLCs](https://en.wikipedia.org/wiki/Programmable_logic_controller),
on my computer. They provided us with an installer for version `8.0.0`, and
since I did not really want to sign up on Siemens' official website to get a
newer version, that's what I used. The website says that the software still
supports Windows XP, but it wasn't working on my system.

When I tried to run the installer, I got an error message concerning Windows
error 193 while loading Java VM.

I needed to install Java on the system. Since the new versions of Java do not
seem to support Windows XP anymore, I needed to find an older one. After a bit
of googling, I found this:
[Where can I find old versions of the JDK and JRE?](https://stackoverflow.com/questions/1460554/where-can-i-find-old-versions-of-the-jdk-and-jre).
I copied the new official download link at
https://www.java.com/en/download/
and replaced the bundle ID with the one from the table. I downloaded a really
old version, `jre-6u23-windows-i586.exe`. Since the VM is not
connected to the Internet and I don't use it all that much, I don't really
care about the security concerns arising from running an old, out-of-date
version of Java.

I grabbed [7-zip](https://www.7-zip.org/) (which still supports Windows XP in
2021!) and extracted the `setup.exe` file for LOGO! Soft Comfort.
In the extracted directory, I then ran
```batch
Windows\Setup.exe LAX_VM "C:\Program Files\Java\jre6\bin\java.exe"
```

After the installation had completed, I created a file called
`logo.bat` on the Desktop with the following contents:
```batch
@echo off
"C:\Program Files\Siemens\LOGOComfort_V8(Demo)\Start.exe" LAX_VM "C:\Program Files\Java\jre6\bin\java.exe"
```
To start the program, I just double-click the `logo.bat` file.
