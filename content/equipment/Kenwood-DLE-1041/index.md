---
draft: false
title: "Kenwood DLE 1041 True RMS Programmable Multimeter"
date: 2022-10-02T17:28:40+02:00
description: "12000 counts 0.04&nbsp;% benchtop digital multimeter with RS-232 interface"
featured_image: "Kenwood-DLE-1041.jpg"
tags: ["DMM"]
categories: ["electronics"]
math: true
---
{{< todo >}}
- TODO calibration
- TODO RS232 adapter board
{{< /todo >}}

{{< figure-resize src="Kenwood-DLE-1041.jpg" width="100%" resize="800x800 q75"
    alt="Front panel of Kenwood DLE 1041 benchtop true RMS programmable multimeter"
>}}

I have bought this (pre-owned) digital multimeter, mainly for it's resolution
(12000 counts) and accuracy (0.04&nbsp;%). It is a very obscure device.
I couldn't find any information about it online, aside from the instruction
manual and a single thread on the EEVblog forums with no responses.

After some time I found out that it looks suspiciously like the
**Aim-TTi 1705**. There's much more information available about that.

I'm quite happy with it. One thing I'm not fond of is it's slow startup
time. It takes about 6.5&nbsp;s to fully initialize after being powered on.
It also has a quite slow autorange function, especially noticeable when
measuring resistance.

{{< image-gallery-list "Kenwood DLE 1041 true RMS programmable multimeter"
    "Kenwood-DLE-1041-back.jpg" "Kenwood-DLE-1041-bot.jpg"
>}}

## ARC protocol
The device uses a protocol called Addressable RS-232 Chain (ARC). It seems to
be a custom protocol used by Aim-TTi devices. The pinout of the DB-9 connector
is non-standard, you need to only connect pins 2, 3 and 5 through to the
computer (more information can be found in the manual).

Initially, I had trouble communicating with the device. The `Print` button on
the front panel worked, but I couldn't get it to receive any instructions.
I have
[downloaded ARC-Talk](https://www.eevblog.com/forum/buysellwanted/(wanted)-tti-arc-windows-software/),
a DOS program used for communicating with devices that speak ARC. Once I was
able to make my DMM work with that, I proceeded to write my own Python program
used for further testing on a more modern GNU/Linux operating system.

```python
#!/usr/bin/env python3
import serial

def main():
    ser = serial.Serial("/dev/ttyUSB0", 9600, dsrdtr=False, xonxoff=True)
    ser.write(b"\x04")
    ser.write(b"*IDN?\n")

    while True:
        try:
            print(ser.read(1))
        except KeyboardInterrupt:
            ser.write(f"{input('> ')}\n".encode("ascii"))

if __name__ == "__main__":
    main()
```

This program sends a single ARC instruction at the beginning, `0x04` Lock
Non-Addressable mode (LNA). However, further experimentation showed that I
don't even need to do that, the program seems to work just fine after
commenting that line out.

The real issue seemed to be that I was doing my initial testing with
`minicom`, which only sent CR (`0x0D`) when I pressed return, while the DMM
was expecting a line feed (LF, `0x0A`).

There are three possible solutions to that problem:
- Use a different terminal program. E.g. [`tio`](https://github.com/tio/tio):
  ```sh
  tio -f soft -e -b 9600 -m OCRNL /dev/ttyUSB0
  ```

- Always press `ctrl-J` instead of the return key in `minicom` to send `LF` instead
  of `CR`.

- Create a character conversion file for `minicom`.
  `ctrl+A O jjjj<CR> O E C 13<CR> <CR> 10<CR> B` and save the file as
  `dle1041.convf`. You can then use a `.minirc.dle1041` like this:
  ```
  # Machine-generated file - use setup menu in minicom to change parameters.
  pu port             /dev/ttyUSB-dle1041
  pu baudrate         9600
  pu rtscts           No
  pu xonxoff          Yes
  pu convf            dle1041.convf
  pu localecho        Yes
  ```


## Power measurement
This is a dual display dual measurement multimeter, meaning that it can
measure voltage and current simultaneously. There even is a `VA` function to
multiply the two values, thus getting a VA reading.

Unfortunately, despite the fact that there is a function called `W`, it only
calculates $P = \frac{V^2}{R}$ (the $R$ value can be programmed in using the
`EDIT` key). So no real power and no power factor ($\cos\varphi$).


## Battery operation
Supposedly, this device can be powered off of 6 C cells. I haven't tested that
yet, but I did make sure the battery compartment was empty when I bought it.
I don't want battery leakage to ruin my meter.

The manual also says that the RS-232 interface only operates if the device is
powered from an AC supply.
