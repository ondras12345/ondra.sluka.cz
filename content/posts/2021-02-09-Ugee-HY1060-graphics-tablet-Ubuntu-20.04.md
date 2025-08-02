---
draft: false
date: 2021-02-09T15:47:41+02:00
title: "Making an Ugee HY1060 Graphics Tablet (UC-LOIC TABLET 1060) Work in Ubuntu 20.04"
description: >-
  Ubuntu 20.04 shipped with a broken version of `60-input-id.hwdb` that did
  not work with my graphics tablet and I spent so much time trying to fix it
  that I made a post about it.
featured_image: ""
tags:
  - GNU/Linux
categories:
  - software
series:
---

This was originally meant to be a post
on [Ubuntu forums](https://ubuntuforums.org).
However, because I found the solution myself, I'll post it here.

I have a Ugee HY1060 graphics tablet that works just fine with a computer
running Lubuntu 19.10.
However, when I plug it into my laptop running Ubuntu 20.04, it does not move
the cursor. After doing side-by-side comparison between the machines, I was
able to figure out what the issue is. Jump to the **end of this post if
you want to see the solution**.

To make it clear which machine I run the commands on,
I will use the `$` shell prompt for \*buntu 19.10
and `>` for Ubuntu 20.04.

Working machine:
```console
$ uname -a
Linux pc1 5.3.0-64-generic #58-Ubuntu SMP Fri Jul 10 19:33:51 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux

$ lsb_release -a
No LSB modules are available.
Distributor ID:    Ubuntu
Description:    Ubuntu 19.10
Release:    19.10
Codename:    eoan

$ libinput --version
1.14.1
```

Not working:
```console
> uname -a
Linux ondra-HP 5.8.0-41-generic #46~20.04.1-Ubuntu SMP Mon Jan 18 17:52:23 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux

> lsb_release -a
No LSB modules are available.
Distributor ID:    Ubuntu
Description:    Ubuntu 20.04.2 LTS
Release:    20.04
Codename:    focal

> libinput --version
1.15.5
```

I'm pretty sure this has nothing to do with the differences between Ubuntu and
Lubuntu as I've tried this in a VM with Lubuntu 20.04 live ISO and it did not
work, while it was working with Lubuntu 19.10 in the same VM.

<details>
  <summary><code>lsusb</code></summary>

```console
> lsusb
Bus 002 Device 002: ID 8087:8000 Intel Corp.
Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 002: ID 8087:8008 Intel Corp.
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 004 Device 003: ID 174c:1153 ASMedia Technology Inc. ASM1153 SATA 3Gb/s bridge
Bus 004 Device 002: ID 0424:5434 Microchip Technology, Inc. (formerly SMSC) Hub
Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 003 Device 005: ID 04f2:b3ec Chicony Electronics Co., Ltd HP HD Webcam
Bus 003 Device 003: ID 138a:003f Validity Sensors, Inc. VFS495 Fingerprint Reader
Bus 003 Device 012: ID 5543:0081 UC-Logic Technology Corp. TABLET 1060
Bus 003 Device 008: ID 8087:07da Intel Corp.
Bus 003 Device 009: ID 4321:6789  
Bus 003 Device 011: ID 046d:c52b Logitech, Inc. Unifying Receiver
Bus 003 Device 010: ID 0461:4de2 Primax Electronics, Ltd TABLET 1060
Bus 003 Device 007: ID 0d8c:0102 C-Media Electronics, Inc. CM106 Like Sound Device
Bus 003 Device 004: ID 1a40:0201 Terminus Technology Inc. FE 2.1 7-port Hub
Bus 003 Device 002: ID 0424:5434 Microchip Technology, Inc. (formerly SMSC) Hub
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

> lsusb -v -s 12
Bus 003 Device 012: ID 5543:0081 UC-Logic Technology Corp. TABLET 1060
Couldn't open device, some information will be missing
Device Descriptor:
  bLength                18
  bDescriptorType         1
  bcdUSB               1.10
  bDeviceClass            0
  bDeviceSubClass         0
  bDeviceProtocol         0
  bMaxPacketSize0        64
  idVendor           0x5543 UC-Logic Technology Corp.
  idProduct          0x0081
  bcdDevice            0.00
  iManufacturer           5
  iProduct                6
  iSerial                 0
  bNumConfigurations      1
  Configuration Descriptor:
    bLength                 9
    bDescriptorType         2
    wTotalLength       0x0054
    bNumInterfaces          3
    bConfigurationValue     1
    iConfiguration          0
    bmAttributes         0xa0
      (Bus Powered)
      Remote Wakeup
    MaxPower              100mA
    Interface Descriptor:
      bLength                 9
      bDescriptorType         4
      bInterfaceNumber        0
      bAlternateSetting       0
      bNumEndpoints           1
      bInterfaceClass         3 Human Interface Device
      bInterfaceSubClass      1 Boot Interface Subclass
      bInterfaceProtocol      2 Mouse
      iInterface              0
        HID Device Descriptor:
          bLength                 9
          bDescriptorType        33
          bcdHID               1.11
          bCountryCode            0 Not supported
          bNumDescriptors         1
          bDescriptorType        34 Report
          wDescriptorLength     179
         Report Descriptors:
           ** UNAVAILABLE **
      Endpoint Descriptor:
        bLength                 7
        bDescriptorType         5
        bEndpointAddress     0x81  EP 1 IN
        bmAttributes            3
          Transfer Type            Interrupt
          Synch Type               None
          Usage Type               Data
        wMaxPacketSize     0x0008  1x 8 bytes
        bInterval               2
    Interface Descriptor:
      bLength                 9
      bDescriptorType         4
      bInterfaceNumber        1
      bAlternateSetting       0
      bNumEndpoints           1
      bInterfaceClass         3 Human Interface Device
      bInterfaceSubClass      1 Boot Interface Subclass
      bInterfaceProtocol      2 Mouse
      iInterface              0
        HID Device Descriptor:
          bLength                 9
          bDescriptorType        33
          bcdHID               1.11
          bCountryCode            0 Not supported
          bNumDescriptors         1
          bDescriptorType        34 Report
          wDescriptorLength     181
         Report Descriptors:
           ** UNAVAILABLE **
      Endpoint Descriptor:
        bLength                 7
        bDescriptorType         5
        bEndpointAddress     0x82  EP 2 IN
        bmAttributes            3
          Transfer Type            Interrupt
          Synch Type               None
          Usage Type               Data
        wMaxPacketSize     0x0008  1x 8 bytes
        bInterval               2
    Interface Descriptor:
      bLength                 9
      bDescriptorType         4
      bInterfaceNumber        2
      bAlternateSetting       0
      bNumEndpoints           1
      bInterfaceClass         3 Human Interface Device
      bInterfaceSubClass      0
      bInterfaceProtocol      0
      iInterface              0
        HID Device Descriptor:
          bLength                 9
          bDescriptorType        33
          bcdHID               1.11
          bCountryCode            0 Not supported
          bNumDescriptors         1
          bDescriptorType        34 Report
          wDescriptorLength      92
         Report Descriptors:
           ** UNAVAILABLE **
      Endpoint Descriptor:
        bLength                 7
        bDescriptorType         5
        bEndpointAddress     0x83  EP 3 IN
        bmAttributes            3
          Transfer Type            Interrupt
          Synch Type               None
          Usage Type               Data
        wMaxPacketSize     0x0008  1x 8 bytes
        bInterval               8
```
</details>

<details>
  <summary><code>xinput</code></summary>

```console
> xinput
⎡ Virtual core pointer                        id=2    [master pointer  (3)]
⎜   ↳ Virtual core XTEST pointer                  id=4    [slave  pointer  (2)]
⎜   ↳ USB Optical Mouse                           id=10    [slave  pointer  (2)]
⎜   ↳ Logitech K520                               id=11    [slave  pointer  (2)]
⎜   ↳ Logitech M310/M310t                         id=12    [slave  pointer  (2)]
⎜   ↳ WingsChip Gaming Mouse                      id=14    [slave  pointer  (2)]
⎜   ↳ WingsChip Gaming Mouse Consumer Control     id=15    [slave  pointer  (2)]
⎜   ↳ SynPS/2 Synaptics TouchPad                  id=18    [slave  pointer  (2)]
⎜   ↳ UC-LOIC TABLET 1060 Keyboard                id=23    [slave  pointer  (2)]
⎜   ↳ UC-LOIC TABLET 1060 Consumer Control        id=24    [slave  pointer  (2)]
⎜   ↳ UC-LOIC TABLET 1060 System Control          id=25    [slave  pointer  (2)]
⎜   ↳ UC-LOIC TABLET 1060 Mouse                   id=26    [slave  pointer  (2)]
⎜   ↳ UC-LOIC TABLET 1060 Pad                     id=27    [slave  pointer  (2)]
⎜   ↳ UC-LOIC TABLET 1060 Pen                     id=28    [slave  pointer  (2)]
⎣ Virtual core keyboard                       id=3    [master keyboard (2)]
    ↳ Virtual core XTEST keyboard                 id=5    [slave  keyboard (3)]
    ↳ Power Button                                id=6    [slave  keyboard (3)]
    ↳ Video Bus                                   id=7    [slave  keyboard (3)]
    ↳ Sleep Button                                id=8    [slave  keyboard (3)]
    ↳ USB Sound Device                            id=9    [slave  keyboard (3)]
    ↳ WingsChip Gaming Mouse                      id=13    [slave  keyboard (3)]
    ↳ WingsChip Gaming Mouse System Control       id=16    [slave  keyboard (3)]
    ↳ AT Translated Set 2 keyboard                id=17    [slave  keyboard (3)]
    ↳ HP Wireless hotkeys                         id=19    [slave  keyboard (3)]
    ↳ HP WMI hotkeys                              id=20    [slave  keyboard (3)]
    ↳ Logitech K520                               id=21    [slave  keyboard (3)]
    ↳ WingsChip Gaming Mouse Consumer Control     id=22    [slave  keyboard (3)]

> xinput list 28
UC-LOIC TABLET 1060 Pen                     id=28    [slave  pointer  (2)]
    Reporting 8 classes:
        Class originated from: 28. Type: XIButtonClass
        Buttons supported: 4
        Button labels: None None None None
        Button state:
        Class originated from: 28. Type: XIValuatorClass
        Detail for Valuator 0:
          Label: Abs X
          Range: 0.000000 - 16777215.000000
          Resolution: 0 units/m
          Mode: absolute
          Current value: 0.000000
        Class originated from: 28. Type: XIValuatorClass
        Detail for Valuator 1:
          Label: Abs Y
          Range: 0.000000 - 16777215.000000
          Resolution: 0 units/m
          Mode: absolute
          Current value: 0.000000
        Class originated from: 28. Type: XIValuatorClass
        Detail for Valuator 2:
          Label: Abs Pressure
          Range: 0.000000 - 16777215.000000
          Resolution: 0 units/m
          Mode: absolute
          Current value: 0.000000
        Class originated from: 28. Type: XIValuatorClass
        Detail for Valuator 3:
          Label: None
          Range: 0.000000 - 4096.000000
          Resolution: 0 units/m
          Mode: absolute
          Current value: 0.000000
        Class originated from: 28. Type: XIValuatorClass
        Detail for Valuator 4:
          Label: None
          Range: 0.000000 - 4096.000000
          Resolution: 0 units/m
          Mode: absolute
          Current value: 0.000000
        Class originated from: 28. Type: XIValuatorClass
        Detail for Valuator 5:
          Label: Abs Wheel
          Range: 0.000000 - 71.000000
          Resolution: 0 units/m
          Mode: absolute
          Current value: 0.000000
        Class originated from: 28. Type: XIValuatorClass
        Detail for Valuator 6:
          Label: None
          Range: 0.000000 - 71.000000
          Resolution: 0 units/m
          Mode: absolute
          Current value: 0.000000
```
</details>

<details>
<summary>Relevant part of Xorg log</summary>

```text
> cat ~/.local/share/xorg/Xorg.0.log
...
[ 48281.146] (II) config/udev: Adding input device (unnamed) (/dev/input/event256)
[ 48281.148] (**) (unnamed): Applying InputClass "libinput tablet catchall"
[ 48281.148] (II) Using input driver 'libinput' for '(unnamed)'
[ 48281.149] (EE) systemd-logind: failed to take device /dev/input/event256: No such device
[ 48281.150] (**) (unnamed): always reports core events
[ 48281.150] (**) Option "Device" "/dev/input/event256"
[ 48281.150] (**) Option "_source" "server/udev"
[ 48281.150] (EE) client bug: Invalid path /dev/input/event256
[ 48281.150] (EE) libinput: (unnamed): Failed to create a device for /dev/input/event256
[ 48281.156] (EE) PreInit returned 2 for "(unnamed)"
[ 48281.156] (II) UnloadModule: "libinput"
[ 48281.194] (II) config/udev: Adding input device (unnamed) (/dev/input/event31)
[ 48281.194] (**) (unnamed): Applying InputClass "libinput tablet catchall"
[ 48281.194] (II) Using input driver 'libinput' for '(unnamed)'
[ 48281.195] (II) systemd-logind: got fd for /dev/input/event31 13:95 fd 99 paused 0
[ 48281.195] (**) (unnamed): always reports core events
[ 48281.195] (**) Option "Device" "/dev/input/event31"
[ 48281.195] (**) Option "_source" "server/udev"
[ 48281.200] (II) event31 - UC-LOIC TABLET 1060 System Control: is tagged by udev as: Tablet
[ 48281.252] (EE) event31 - UC-LOIC TABLET 1060 System Control: libinput bug: missing tablet capabilities: xy pen btn-stylus resolution. Ignoring this device.
[ 48281.252] (II) event31 - UC-LOIC TABLET 1060 System Control: device is a tablet
[ 48281.256] (II) event31 - failed to create input device '/dev/input/event31'.
[ 48281.256] (EE) libinput: (unnamed): Failed to create a device for /dev/input/event31
[ 48281.256] (EE) PreInit returned 2 for "(unnamed)"
[ 48281.256] (II) UnloadModule: "libinput"
[ 48281.257] (II) systemd-logind: releasing fd for 13:95
[ 48281.305] (II) config/udev: Adding input device (unnamed) (/dev/input/event29)
[ 48281.305] (**) (unnamed): Applying InputClass "libinput tablet catchall"
[ 48281.305] (II) Using input driver 'libinput' for '(unnamed)'
[ 48281.307] (II) systemd-logind: got fd for /dev/input/event29 13:93 fd 99 paused 0
[ 48281.307] (**) (unnamed): always reports core events
[ 48281.307] (**) Option "Device" "/dev/input/event29"
[ 48281.307] (**) Option "_source" "server/udev"
[ 48281.339] (II) event29 - UC-LOIC TABLET 1060 Keyboard: is tagged by udev as: Keyboard Tablet TabletPad
[ 48281.367] (II) event29 - UC-LOIC TABLET 1060 Keyboard: tablet 'UC-LOIC TABLET 1060 Keyboard' unknown to libwacom
[ 48281.381] (II) event29 - UC-LOIC TABLET 1060 Keyboard: device is a tablet pad
[ 48281.383] (II) event29 - UC-LOIC TABLET 1060 Keyboard: device removed
[ 48281.383] (**) Option "config_info" "udev:/sys/devices/pci0000:00/0000:00:14.0/usb3/3-3/3-3:1.1/0003:5543:0081.000D/input/input51/event29"
[ 48281.383] (II) XINPUT: Adding extended input device "(unnamed)" (type: PAD, id 23)
[ 48281.384] (**) Option "AccelerationScheme" "none"
[ 48281.387] (**) (unnamed): (accel) selected scheme none/0
[ 48281.387] (**) (unnamed): (accel) acceleration factor: 2.000
[ 48281.387] (**) (unnamed): (accel) acceleration threshold: 4
[ 48281.389] (II) event29 - UC-LOIC TABLET 1060 Keyboard: is tagged by udev as: Keyboard Tablet TabletPad
[ 48281.411] (II) event29 - UC-LOIC TABLET 1060 Keyboard: tablet 'UC-LOIC TABLET 1060 Keyboard' unknown to libwacom
[ 48281.426] (II) event29 - UC-LOIC TABLET 1060 Keyboard: device is a tablet pad
[ 48281.430] (II) config/udev: Adding input device (unnamed) (/dev/input/event30)
[ 48281.430] (**) (unnamed): Applying InputClass "libinput tablet catchall"
[ 48281.430] (II) Using input driver 'libinput' for '(unnamed)'
[ 48281.432] (II) systemd-logind: got fd for /dev/input/event30 13:94 fd 102 paused 0
[ 48281.432] (**) (unnamed): always reports core events
[ 48281.432] (**) Option "Device" "/dev/input/event30"
[ 48281.433] (**) Option "_source" "server/udev"
[ 48281.434] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: is tagged by udev as: Keyboard Tablet TabletPad
[ 48281.457] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: tablet 'UC-LOIC TABLET 1060 Consumer Control' unknown to libwacom
[ 48281.468] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: device is a tablet pad
[ 48281.468] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: device removed
[ 48281.468] (**) Option "config_info" "udev:/sys/devices/pci0000:00/0000:00:14.0/usb3/3-3/3-3:1.2/0003:5543:0081.000E/input/input52/event30"
[ 48281.468] (II) XINPUT: Adding extended input device "(unnamed)" (type: PAD, id 24)
[ 48281.469] (**) Option "AccelerationScheme" "none"
[ 48281.470] (**) (unnamed): (accel) selected scheme none/0
[ 48281.470] (**) (unnamed): (accel) acceleration factor: 2.000
[ 48281.470] (**) (unnamed): (accel) acceleration threshold: 4
[ 48281.472] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: is tagged by udev as: Keyboard Tablet TabletPad
[ 48281.493] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: tablet 'UC-LOIC TABLET 1060 Consumer Control' unknown to libwacom
[ 48281.503] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: device is a tablet pad
[ 48281.504] (II) config/udev: Adding input device (unnamed) (/dev/input/event27)
[ 48281.504] (**) (unnamed): Applying InputClass "libinput tablet catchall"
[ 48281.504] (II) Using input driver 'libinput' for '(unnamed)'
[ 48281.564] (II) systemd-logind: got fd for /dev/input/event27 13:91 fd 104 paused 0
[ 48281.564] (**) (unnamed): always reports core events
[ 48281.565] (**) Option "Device" "/dev/input/event27"
[ 48281.565] (**) Option "_source" "server/udev"
[ 48281.569] (II) event27 - UC-LOIC TABLET 1060 Pad: is tagged by udev as: Tablet TabletPad
[ 48281.590] (II) event27 - UC-LOIC TABLET 1060 Pad: tablet 'UC-LOIC TABLET 1060 Pad' unknown to libwacom
[ 48281.600] (II) event27 - UC-LOIC TABLET 1060 Pad: device is a tablet pad
[ 48281.600] (II) event27 - UC-LOIC TABLET 1060 Pad: device removed
[ 48281.600] (**) Option "config_info" "udev:/sys/devices/pci0000:00/0000:00:14.0/usb3/3-3/3-3:1.0/0003:5543:0081.000C/input/input49/event27"
[ 48281.600] (II) XINPUT: Adding extended input device "(unnamed)" (type: PAD, id 25)
[ 48281.601] (**) Option "AccelerationScheme" "none"
[ 48281.602] (**) (unnamed): (accel) selected scheme none/0
[ 48281.602] (**) (unnamed): (accel) acceleration factor: 2.000
[ 48281.602] (**) (unnamed): (accel) acceleration threshold: 4
[ 48281.603] (II) event27 - UC-LOIC TABLET 1060 Pad: is tagged by udev as: Tablet TabletPad
[ 48281.618] (II) event27 - UC-LOIC TABLET 1060 Pad: tablet 'UC-LOIC TABLET 1060 Pad' unknown to libwacom
[ 48281.626] (II) event27 - UC-LOIC TABLET 1060 Pad: device is a tablet pad
[ 48281.627] (EE) client bug: timer event11 debounce: scheduled expiry is in the past (-34ms), your system is too slow
[ 48281.628] (II) config/udev: Adding input device (unnamed) (/dev/input/event26)
[ 48281.628] (**) (unnamed): Applying InputClass "libinput tablet catchall"
[ 48281.628] (II) Using input driver 'libinput' for '(unnamed)'
[ 48281.629] (II) systemd-logind: got fd for /dev/input/event26 13:90 fd 107 paused 0
[ 48281.629] (**) (unnamed): always reports core events
[ 48281.629] (**) Option "Device" "/dev/input/event26"
[ 48281.629] (**) Option "_source" "server/udev"
[ 48281.631] (II) event26 - UC-LOIC TABLET 1060 Pen: is tagged by udev as: Tablet TabletPad
[ 48281.652] (II) event26 - UC-LOIC TABLET 1060 Pen: tablet 'UC-LOIC TABLET 1060 Pen' unknown to libwacom
[ 48281.661] (II) event26 - UC-LOIC TABLET 1060 Pen: device is a tablet pad
[ 48281.661] (II) event26 - UC-LOIC TABLET 1060 Pen: device removed
[ 48281.661] (**) Option "config_info" "udev:/sys/devices/pci0000:00/0000:00:14.0/usb3/3-3/3-3:1.0/0003:5543:0081.000C/input/input48/event26"
[ 48281.661] (II) XINPUT: Adding extended input device "(unnamed)" (type: PAD, id 26)
[ 48281.662] (**) Option "AccelerationScheme" "none"
[ 48281.663] (**) (unnamed): (accel) selected scheme none/0
[ 48281.663] (**) (unnamed): (accel) acceleration factor: 2.000
[ 48281.663] (**) (unnamed): (accel) acceleration threshold: 4
[ 48281.664] (II) event26 - UC-LOIC TABLET 1060 Pen: is tagged by udev as: Tablet TabletPad
[ 48281.680] (II) event26 - UC-LOIC TABLET 1060 Pen: tablet 'UC-LOIC TABLET 1060 Pen' unknown to libwacom
[ 48281.688] (II) event26 - UC-LOIC TABLET 1060 Pen: device is a tablet pad
[ 48281.689] (EE) client bug: timer event11 debounce short: scheduled expiry is in the past (-5ms), your system is too slow
[ 48281.690] (II) config/udev: Adding input device (unnamed) (/dev/input/event28)
[ 48281.690] (**) (unnamed): Applying InputClass "libinput tablet catchall"
[ 48281.690] (II) Using input driver 'libinput' for '(unnamed)'
[ 48281.749] (II) systemd-logind: got fd for /dev/input/event28 13:92 fd 110 paused 0
[ 48281.749] (**) (unnamed): always reports core events
[ 48281.749] (**) Option "Device" "/dev/input/event28"
[ 48281.749] (**) Option "_source" "server/udev"
[ 48281.754] (II) event28 - UC-LOIC TABLET 1060 Mouse: is tagged by udev as: Mouse Tablet TabletPad
[ 48281.776] (II) event28 - UC-LOIC TABLET 1060 Mouse: tablet 'UC-LOIC TABLET 1060 Mouse' unknown to libwacom
[ 48281.785] (II) event28 - UC-LOIC TABLET 1060 Mouse: device is a tablet pad
[ 48281.785] (II) event28 - UC-LOIC TABLET 1060 Mouse: device removed
[ 48281.785] (**) Option "config_info" "udev:/sys/devices/pci0000:00/0000:00:14.0/usb3/3-3/3-3:1.1/0003:5543:0081.000D/input/input50/event28"
[ 48281.785] (II) XINPUT: Adding extended input device "(unnamed)" (type: PAD, id 27)
[ 48281.786] (**) Option "AccelerationScheme" "none"
[ 48281.787] (**) (unnamed): (accel) selected scheme none/0
[ 48281.787] (**) (unnamed): (accel) acceleration factor: 2.000
[ 48281.787] (**) (unnamed): (accel) acceleration threshold: 4
[ 48281.788] (II) event28 - UC-LOIC TABLET 1060 Mouse: is tagged by udev as: Mouse Tablet TabletPad
[ 48281.803] (II) event28 - UC-LOIC TABLET 1060 Mouse: tablet 'UC-LOIC TABLET 1060 Mouse' unknown to libwacom
[ 48281.811] (II) event28 - UC-LOIC TABLET 1060 Mouse: device is a tablet pad
[ 48281.812] (II) config/udev: removing device (unnamed)
[ 48281.812] (**) Option "fd" "110"
[ 48281.812] (II) event28 - UC-LOIC TABLET 1060 Mouse: device removed
[ 48281.814] (II) UnloadModule: "libinput"
[ 48281.815] (II) systemd-logind: releasing fd for 13:92
[ 48281.844] (II) config/udev: removing device (unnamed)
[ 48281.844] (**) Option "fd" "104"
[ 48281.844] (II) event27 - UC-LOIC TABLET 1060 Pad: device removed
[ 48281.847] (II) UnloadModule: "libinput"
[ 48281.847] (II) systemd-logind: releasing fd for 13:91
[ 48281.885] (II) config/udev: removing device (unnamed)
[ 48281.885] (**) Option "fd" "102"
[ 48281.885] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: device removed
[ 48281.890] (II) UnloadModule: "libinput"
[ 48281.890] (II) systemd-logind: releasing fd for 13:94
[ 48281.921] (II) config/udev: removing device (unnamed)
[ 48281.921] (**) Option "fd" "99"
[ 48281.921] (II) event29 - UC-LOIC TABLET 1060 Keyboard: device removed
[ 48281.923] (II) UnloadModule: "libinput"
[ 48281.923] (II) systemd-logind: releasing fd for 13:93
[ 48281.948] (II) config/udev: removing device (unnamed)
[ 48281.948] (**) Option "fd" "107"
[ 48281.948] (II) event26 - UC-LOIC TABLET 1060 Pen: device removed
[ 48281.950] (II) UnloadModule: "libinput"
[ 48281.950] (II) systemd-logind: releasing fd for 13:90
[ 48281.981] (II) config/udev: Adding input device UC-LOIC TABLET 1060 Mouse (/dev/input/mouse5)
[ 48281.982] (II) No input driver specified, ignoring this device.
[ 48281.982] (II) This device may have been added with another device file.
[ 48281.983] (II) config/udev: Adding input device UC-LOIC TABLET 1060 Pad (/dev/input/js1)
[ 48281.983] (II) No input driver specified, ignoring this device.
[ 48281.983] (II) This device may have been added with another device file.
[ 48281.984] (II) config/udev: Adding input device UC-LOIC TABLET 1060 Pen (/dev/input/mouse4)
[ 48281.984] (II) No input driver specified, ignoring this device.
[ 48281.984] (II) This device may have been added with another device file.
[ 48281.985] (II) config/udev: Adding input device UC-LOIC TABLET 1060 Keyboard (/dev/input/event29)
[ 48281.985] (**) UC-LOIC TABLET 1060 Keyboard: Applying InputClass "libinput keyboard catchall"
[ 48281.985] (**) UC-LOIC TABLET 1060 Keyboard: Applying InputClass "libinput tablet catchall"
[ 48281.985] (II) Using input driver 'libinput' for 'UC-LOIC TABLET 1060 Keyboard'
[ 48281.986] (II) systemd-logind: got fd for /dev/input/event29 13:93 fd 99 paused 0
[ 48281.986] (**) UC-LOIC TABLET 1060 Keyboard: always reports core events
[ 48281.986] (**) Option "Device" "/dev/input/event29"
[ 48281.986] (**) Option "_source" "server/udev"
[ 48281.987] (II) event29 - UC-LOIC TABLET 1060 Keyboard: is tagged by udev as: Keyboard Tablet TabletPad
[ 48282.009] (II) event29 - UC-LOIC TABLET 1060 Keyboard: tablet 'UC-LOIC TABLET 1060 Keyboard' unknown to libwacom
[ 48282.019] (II) event29 - UC-LOIC TABLET 1060 Keyboard: device is a tablet pad
[ 48282.019] (II) event29 - UC-LOIC TABLET 1060 Keyboard: device removed
[ 48282.019] (**) Option "config_info" "udev:/sys/devices/pci0000:00/0000:00:14.0/usb3/3-3/3-3:1.2/0003:5543:0081.000E/input/input59/event29"
[ 48282.019] (II) XINPUT: Adding extended input device "UC-LOIC TABLET 1060 Keyboard" (type: PAD, id 23)
[ 48282.020] (**) Option "AccelerationScheme" "none"
[ 48282.021] (**) UC-LOIC TABLET 1060 Keyboard: (accel) selected scheme none/0
[ 48282.021] (**) UC-LOIC TABLET 1060 Keyboard: (accel) acceleration factor: 2.000
[ 48282.021] (**) UC-LOIC TABLET 1060 Keyboard: (accel) acceleration threshold: 4
[ 48282.022] (II) event29 - UC-LOIC TABLET 1060 Keyboard: is tagged by udev as: Keyboard Tablet TabletPad
[ 48282.041] (II) event29 - UC-LOIC TABLET 1060 Keyboard: tablet 'UC-LOIC TABLET 1060 Keyboard' unknown to libwacom
[ 48282.049] (II) event29 - UC-LOIC TABLET 1060 Keyboard: device is a tablet pad
[ 48282.051] (II) config/udev: Adding input device UC-LOIC TABLET 1060 Consumer Control (/dev/input/event30)
[ 48282.051] (**) UC-LOIC TABLET 1060 Consumer Control: Applying InputClass "libinput keyboard catchall"
[ 48282.051] (**) UC-LOIC TABLET 1060 Consumer Control: Applying InputClass "libinput tablet catchall"
[ 48282.051] (II) Using input driver 'libinput' for 'UC-LOIC TABLET 1060 Consumer Control'
[ 48282.052] (II) systemd-logind: got fd for /dev/input/event30 13:94 fd 102 paused 0
[ 48282.052] (**) UC-LOIC TABLET 1060 Consumer Control: always reports core events
[ 48282.052] (**) Option "Device" "/dev/input/event30"
[ 48282.052] (**) Option "_source" "server/udev"
[ 48282.054] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: is tagged by udev as: Keyboard Tablet TabletPad
[ 48282.073] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: tablet 'UC-LOIC TABLET 1060 Consumer Control' unknown to libwacom
[ 48282.083] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: device is a tablet pad
[ 48282.083] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: device removed
[ 48282.083] (**) Option "config_info" "udev:/sys/devices/pci0000:00/0000:00:14.0/usb3/3-3/3-3:1.2/0003:5543:0081.000E/input/input60/event30"
[ 48282.083] (II) XINPUT: Adding extended input device "UC-LOIC TABLET 1060 Consumer Control" (type: PAD, id 24)
[ 48282.083] (**) Option "AccelerationScheme" "none"
[ 48282.084] (**) UC-LOIC TABLET 1060 Consumer Control: (accel) selected scheme none/0
[ 48282.084] (**) UC-LOIC TABLET 1060 Consumer Control: (accel) acceleration factor: 2.000
[ 48282.084] (**) UC-LOIC TABLET 1060 Consumer Control: (accel) acceleration threshold: 4
[ 48282.086] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: is tagged by udev as: Keyboard Tablet TabletPad
[ 48282.101] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: tablet 'UC-LOIC TABLET 1060 Consumer Control' unknown to libwacom
[ 48282.108] (II) event30 - UC-LOIC TABLET 1060 Consumer Control: device is a tablet pad
[ 48282.110] (II) config/udev: Adding input device UC-LOIC TABLET 1060 System Control (/dev/input/event31)
[ 48282.110] (**) UC-LOIC TABLET 1060 System Control: Applying InputClass "libinput keyboard catchall"
[ 48282.110] (**) UC-LOIC TABLET 1060 System Control: Applying InputClass "libinput tablet catchall"
[ 48282.110] (II) Using input driver 'libinput' for 'UC-LOIC TABLET 1060 System Control'
[ 48282.111] (II) systemd-logind: got fd for /dev/input/event31 13:95 fd 104 paused 0
[ 48282.111] (**) UC-LOIC TABLET 1060 System Control: always reports core events
[ 48282.111] (**) Option "Device" "/dev/input/event31"
[ 48282.111] (**) Option "_source" "server/udev"
[ 48282.113] (II) event31 - UC-LOIC TABLET 1060 System Control: is tagged by udev as: Keyboard Tablet TabletPad
[ 48282.135] (II) event31 - UC-LOIC TABLET 1060 System Control: tablet 'UC-LOIC TABLET 1060 System Control' unknown to libwacom
[ 48282.145] (II) event31 - UC-LOIC TABLET 1060 System Control: device is a tablet pad
[ 48282.145] (II) event31 - UC-LOIC TABLET 1060 System Control: device removed
[ 48282.145] (**) Option "config_info" "udev:/sys/devices/pci0000:00/0000:00:14.0/usb3/3-3/3-3:1.2/0003:5543:0081.000E/input/input61/event31"
[ 48282.145] (II) XINPUT: Adding extended input device "UC-LOIC TABLET 1060 System Control" (type: PAD, id 25)
[ 48282.146] (**) Option "AccelerationScheme" "none"
[ 48282.146] (**) UC-LOIC TABLET 1060 System Control: (accel) selected scheme none/0
[ 48282.147] (**) UC-LOIC TABLET 1060 System Control: (accel) acceleration factor: 2.000
[ 48282.147] (**) UC-LOIC TABLET 1060 System Control: (accel) acceleration threshold: 4
[ 48282.148] (II) event31 - UC-LOIC TABLET 1060 System Control: is tagged by udev as: Keyboard Tablet TabletPad
[ 48282.163] (II) event31 - UC-LOIC TABLET 1060 System Control: tablet 'UC-LOIC TABLET 1060 System Control' unknown to libwacom
[ 48282.171] (II) event31 - UC-LOIC TABLET 1060 System Control: device is a tablet pad
[ 48282.172] (II) config/udev: Adding input device UC-LOIC TABLET 1060 Mouse (/dev/input/event28)
[ 48282.173] (**) UC-LOIC TABLET 1060 Mouse: Applying InputClass "libinput pointer catchall"
[ 48282.173] (**) UC-LOIC TABLET 1060 Mouse: Applying InputClass "libinput tablet catchall"
[ 48282.173] (II) Using input driver 'libinput' for 'UC-LOIC TABLET 1060 Mouse'
[ 48282.232] (II) systemd-logind: got fd for /dev/input/event28 13:92 fd 107 paused 0
[ 48282.232] (**) UC-LOIC TABLET 1060 Mouse: always reports core events
[ 48282.233] (**) Option "Device" "/dev/input/event28"
[ 48282.233] (**) Option "_source" "server/udev"
[ 48282.237] (II) event28 - UC-LOIC TABLET 1060 Mouse: is tagged by udev as: Mouse Tablet TabletPad
[ 48282.260] (II) event28 - UC-LOIC TABLET 1060 Mouse: tablet 'UC-LOIC TABLET 1060 Mouse' unknown to libwacom
[ 48282.271] (II) event28 - UC-LOIC TABLET 1060 Mouse: device is a tablet pad
[ 48282.272] (II) event28 - UC-LOIC TABLET 1060 Mouse: device removed
[ 48282.272] (**) Option "config_info" "udev:/sys/devices/pci0000:00/0000:00:14.0/usb3/3-3/3-3:1.1/0003:5543:0081.000D/input/input57/event28"
[ 48282.272] (II) XINPUT: Adding extended input device "UC-LOIC TABLET 1060 Mouse" (type: PAD, id 26)
[ 48282.272] (**) Option "AccelerationScheme" "none"
[ 48282.273] (**) UC-LOIC TABLET 1060 Mouse: (accel) selected scheme none/0
[ 48282.273] (**) UC-LOIC TABLET 1060 Mouse: (accel) acceleration factor: 2.000
[ 48282.273] (**) UC-LOIC TABLET 1060 Mouse: (accel) acceleration threshold: 4
[ 48282.274] (II) event28 - UC-LOIC TABLET 1060 Mouse: is tagged by udev as: Mouse Tablet TabletPad
[ 48282.291] (II) event28 - UC-LOIC TABLET 1060 Mouse: tablet 'UC-LOIC TABLET 1060 Mouse' unknown to libwacom
[ 48282.299] (II) event28 - UC-LOIC TABLET 1060 Mouse: device is a tablet pad
[ 48282.301] (II) config/udev: Adding input device UC-LOIC TABLET 1060 Pad (/dev/input/event27)
[ 48282.301] (**) UC-LOIC TABLET 1060 Pad: Applying InputClass "libinput tablet catchall"
[ 48282.301] (II) Using input driver 'libinput' for 'UC-LOIC TABLET 1060 Pad'
[ 48282.360] (II) systemd-logind: got fd for /dev/input/event27 13:91 fd 110 paused 0
[ 48282.361] (**) UC-LOIC TABLET 1060 Pad: always reports core events
[ 48282.361] (**) Option "Device" "/dev/input/event27"
[ 48282.361] (**) Option "_source" "server/udev"
[ 48282.365] (II) event27 - UC-LOIC TABLET 1060 Pad: is tagged by udev as: Tablet TabletPad
[ 48282.385] (II) event27 - UC-LOIC TABLET 1060 Pad: tablet 'UC-LOIC TABLET 1060 Pad' unknown to libwacom
[ 48282.395] (II) event27 - UC-LOIC TABLET 1060 Pad: device is a tablet pad
[ 48282.395] (II) event27 - UC-LOIC TABLET 1060 Pad: device removed
[ 48282.396] (**) Option "config_info" "udev:/sys/devices/pci0000:00/0000:00:14.0/usb3/3-3/3-3:1.0/0003:5543:0081.000C/input/input56/event27"
[ 48282.396] (II) XINPUT: Adding extended input device "UC-LOIC TABLET 1060 Pad" (type: PAD, id 27)
[ 48282.396] (**) Option "AccelerationScheme" "none"
[ 48282.397] (**) UC-LOIC TABLET 1060 Pad: (accel) selected scheme none/0
[ 48282.397] (**) UC-LOIC TABLET 1060 Pad: (accel) acceleration factor: 2.000
[ 48282.397] (**) UC-LOIC TABLET 1060 Pad: (accel) acceleration threshold: 4
[ 48282.398] (II) event27 - UC-LOIC TABLET 1060 Pad: is tagged by udev as: Tablet TabletPad
[ 48282.413] (II) event27 - UC-LOIC TABLET 1060 Pad: tablet 'UC-LOIC TABLET 1060 Pad' unknown to libwacom
[ 48282.421] (II) event27 - UC-LOIC TABLET 1060 Pad: device is a tablet pad
[ 48282.424] (II) config/udev: Adding input device UC-LOIC TABLET 1060 Pen (/dev/input/event26)
[ 48282.424] (**) UC-LOIC TABLET 1060 Pen: Applying InputClass "libinput tablet catchall"
[ 48282.424] (II) Using input driver 'libinput' for 'UC-LOIC TABLET 1060 Pen'
[ 48282.424] (II) systemd-logind: got fd for /dev/input/event26 13:90 fd 111 paused 0
[ 48282.425] (**) UC-LOIC TABLET 1060 Pen: always reports core events
[ 48282.425] (**) Option "Device" "/dev/input/event26"
[ 48282.425] (**) Option "_source" "server/udev"
[ 48282.427] (II) event26 - UC-LOIC TABLET 1060 Pen: is tagged by udev as: Tablet TabletPad
[ 48282.448] (II) event26 - UC-LOIC TABLET 1060 Pen: tablet 'UC-LOIC TABLET 1060 Pen' unknown to libwacom
[ 48282.458] (II) event26 - UC-LOIC TABLET 1060 Pen: device is a tablet pad
[ 48282.458] (II) event26 - UC-LOIC TABLET 1060 Pen: device removed
[ 48282.459] (**) Option "config_info" "udev:/sys/devices/pci0000:00/0000:00:14.0/usb3/3-3/3-3:1.0/0003:5543:0081.000C/input/input55/event26"
[ 48282.459] (II) XINPUT: Adding extended input device "UC-LOIC TABLET 1060 Pen" (type: PAD, id 28)
[ 48282.459] (**) Option "AccelerationScheme" "none"
[ 48282.460] (**) UC-LOIC TABLET 1060 Pen: (accel) selected scheme none/0
[ 48282.460] (**) UC-LOIC TABLET 1060 Pen: (accel) acceleration factor: 2.000
[ 48282.460] (**) UC-LOIC TABLET 1060 Pen: (accel) acceleration threshold: 4
```
Mainly this part is important:
```text
[ 48282.461] (II) event26 - UC-LOIC TABLET 1060 Pen: is tagged by udev as: Tablet TabletPad
[ 48282.481] (II) event26 - UC-LOIC TABLET 1060 Pen: tablet 'UC-LOIC TABLET 1060 Pen' unknown to libwacom
[ 48282.489] (II) event26 - UC-LOIC TABLET 1060 Pen: device is a tablet pad
[ 48347.333] (II) event26 - UC-LOIC TABLET 1060 Pen: Unhandled EV_ABS event code 0
[ 48347.333] (II) event26 - UC-LOIC TABLET 1060 Pen: Unhandled EV_ABS event code 0x1
[ 48347.339] (II) event26 - UC-LOIC TABLET 1060 Pen: Unhandled EV_ABS event code 0x1
[ 48347.347] (II) event26 - UC-LOIC TABLET 1060 Pen: Unhandled EV_ABS event code 0x1
[ 48347.347] (II) event26 - UC-LOIC TABLET 1060 Pen: Unhandled EV_ABS event code 0x18
[ 48347.353] (II) event26 - UC-LOIC TABLET 1060 Pen: Unhandled EV_ABS event code 0x1
[ 48347.353] (II) event26 - UC-LOIC TABLET 1060 Pen: Unhandled EV_ABS event code 0x18
[ 48347.357] (II) event26 - UC-LOIC TABLET 1060 Pen: Unhandled EV_ABS event code 0x1
[ 48347.357] (II) event26 - UC-LOIC TABLET 1060 Pen: Unhandled EV_ABS event code 0x18
...
```
</details>

When I try to use the stylus, the log gets flooded with "Unhandled EV_ABS event code" messages.

`> sudo evtest` shows events when I move the stylus while
`> sudo libinput debug-events` shows nothing.
On the other machine, both commands show events.

The issue is that the Pen is incorrectly recognized as a Tablet pad.

The problematic rule is here:
```ini
> cat /usr/lib/udev/hwdb.d/60-input-id.hwdb
# This file is part of systemd.
#
# The lookup keys are composed in:
#   60-input-id.rules
#
# Note: The format of the "input-id:" prefix match key is a
# contract between the rules file and the hardware data, it might
# change in later revisions to support more or better matches, it
# is not necessarily expected to be a stable ABI.
#
# Match string formats:
# id-input:modalias:
#
# To add local entries, create a new file
#   /etc/udev/hwdb.d/61-input-id-local.hwdb
# and add your rules there. To load the new rules execute (as root):
#   systemd-hwdb update
#   udevadm trigger /dev/input/eventXX
# where /dev/input/eventXX is the device in question. If in
# doubt, simply use /dev/input/event* to reload all input rules.
#
# If your changes are generally applicable, preferably send them as a pull
# request to
#   https://github.com/systemd/systemd
# or create a bug report on https://github.com/systemd/systemd/issues and
# include your new rules, a description of the device, and the output of
#   udevadm info /dev/input/eventXX.
#
# This file must only be used where the input_id builtin assigns the wrong
# properties or lacks the assignment of some properties. This is almost
# always caused by a device not adhering to the standard of the device's
# type.
#
# Allowed properties are:
#    ID_INPUT
#    ID_INPUT_ACCELEROMETER, ID_INPUT_MOUSE,
#    ID_INPUT_POINTINGSTICK, ID_INPUT_TOUCHSCREEN, ID_INPUT_TOUCHPAD,
#    ID_INPUT_TABLET, ID_INPUT_TABLET_PAD, ID_INPUT_JOYSTICK, ID_INPUT_KEY,
#    ID_INPUT_KEYBOARD, ID_INPUT_SWITCH, ID_INPUT_TRACKBALL
#
#    ID_INPUT
#       * MUST be set when ANY of ID_INPUT_* is set
#       * MUST be unset when ALL of ID_INPUT_*  are unset
#
#    ID_INPUT_TABLET
#       * MUST be set when setting ID_INPUT_TABLET_PAD
#
# Allowed values are 1 and 0 to set or unset, repsectively.
#
# NOT allowed in this file are:
#    ID_INPUT_WIDTH_MM, ID_INPUT_HEIGHT_MM, ID_INPUT_TOUCHPAD_INTEGRATION
#

# Example:
# id-input:modalias:input:b0003v1234pABCD*
#  ID_INPUT_TOUCHPAD=1
#  ID_INPUT=1

# Sort by brand, model

# UC-Logic TABLET 1060N Pad
id-input:modalias:input:b0003v5543p0081*
 ID_INPUT_TABLET=1
 ID_INPUT_TABLET_PAD=1  # This is the line that causes us problems

# XP-PEN STAR 06
id-input:modalias:input:b0003v28bdp0078*
 ID_INPUT_TABLET=1

# Lite-On Tech IBM USB Travel Keyboard with Ultra Nav Mouse
id-input:modalias:input:b0003v04B3p301Ee0100-e0,1,2,4*
 ID_INPUT_POINTINGSTICK=1
```

It does not match just the pad. It matches the pen, too!
For now, this is how I fixed it:
```ini
> cat /etc/udev/hwdb.d/61-input-id-local.hwdb
# see /usr/lib/udev/hwdb.d/60-input-id.hwdb

# UC-Logic TABLET 1060N Pad
# This does not match just the pad. It matches the pen, too!
id-input:modalias:input:b0003v5543p0081*
 ID_INPUT_TABLET=1
 ID_INPUT_TABLET_PAD=0
```

After fixing it, I found this:
[https://github.com/systemd/systemd/issues/17953](https://github.com/systemd/systemd/issues/17953)
