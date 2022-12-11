+++ 
draft = false
date = 2022-12-11T13:38:19+01:00
title = "1602 character LCD backlight color modification"
description = ""
slug = ""
authors = []
tags = []
categories = ["electronics"]
externalLink = ""
series = []
+++
{{< figure-resize resize="800x800" src="featured.jpg" width="100%"
    caption="1602 LCD display modified to have red backlight. The light reflection on the left is happening because the protective foil is still on."
>}}

As a part of the
[Alarm Clock project](https://github.com/ondras12345/AlarmClock),
I looked into character LCD displays with backlight that wouldn't strain my
eyes when used at night. While it is possible to buy display modules with red
backlight, I was under a bit of a time pressure as I had to complete the
project for school. Because I didn't want to wait a month for a new display
to arrive from China, I decided to modify one of the modules I already had.

Although the display appears blue when lit up, the backlight LED is actually
white. It is the "open cell" LCD itself that is blue. This results in
a blue background with white pixels.

I tried shining a red LED through the edge of the light guide plate and it
seemed to work quite well, so I proceeded with a complete mechanical
disassembly of the module. The original backlight LED was glued in place, so I
had to break it off with a pair of pliers. Be careful, the acrylic light guide
is very fragile (don't ask me how I know).

{{< figure-resize src="disassembly.jpg" width="100%" caption="Partially disassembled 1602 LCD." >}}
{{< figure-resize src="BLU-sheet.jpg" width="100%" caption="Peeling off the upper BLU sheet" >}}
{{< figure-resize src="BLU-light-guide-plate.jpg" width="100%"
    caption="1602 LCD with BLU light guide plate removed. Original backlight LED visible on the right side of the light guide plate"
>}}
{{< figure src="parts.svg" width="100%" caption="Parts of a disassembled 1602 LCD module" >}}

The new LED is an L-934LSRC+G (3&nbsp;mm, Uf=1.75&nbsp;V, 36&nbsp;deg,
653&nbsp;nm, 32&nbsp;mcd / 2&nbsp;mA, clear). I did not need to glue it in
place, I just soldered it to the PCB and inserted into the hole in the light
guide plate.

I'm quite happy with how the result turned out.
The red light is getting attenuated as it passes through the blue open cell,
but it is quite bright when it passes through pixels that are turned on.
In my alarm clock project, I even added an additional resistor in series
with the LED, as well as a transistor to take it back out of circuit. This
allows me to have two backlight brightness levels. More information on this
circuit can be found in the
[AlarmClock schematic](https://github.com/ondras12345/AlarmClock-hardware/blob/master/AlarmClock/AlarmClock.pdf).
