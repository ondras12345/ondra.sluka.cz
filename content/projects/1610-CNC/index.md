---
draft: false
title: "1610 CNC"
date: 2022-09-08T00:45:58+02:00
description: "A small CNC machine for PCB milling and laser engraving"
featured_image: "1610-CNC.jpg"
tags: []
categories: ["electronics", "CNC"]
---
{{< figure-resize src="1610-CNC.jpg" width="100%"
    alt="A small CNC machine made from 2020 aluminium profiles and 3D printed parts"
>}}

I own a small Chinese 1610 CNC machine (workspace dimensions 160x100x40&nbsp;mm).
I mostly use it to mill
prototype <abbr title="printed circuit board">PCB</abbr>s,
but it also came with a 500&nbsp;mW laser.

{{< figure-resize src="1610-CNC-PCB-milling.jpg" width="100%"
    alt="A photo of my 1610 CNC machine milling a simple single-sided prototype PCB. Detail of a spinning V-bit cutting an isolation gap around a PCB trace."
    caption="My 1610 CNC machine milling a simple single-sided prototype PCB"
>}}

The machine uses a Woodpecker controller board with free and open-source
[Grbl firmware](https://github.com/gnea/grbl).
You can find [my Grbl configuration](https://github.com/ondras12345/CNC) on my
Github.

To control the machine, I use a piece of free and open-source software called
[Candle](https://github.com/denvi/Candle/). My Candle config can also be found
in the repository linked above.

{{< figure src="Candle-screenshot.png"
    alt="Screenshot of Candle running on Ubuntu. Toolpath visualisation in the middle. Gcode program listing at the bottom. Machine and work XYZ coordinates and status indicator in top right corner. Various controls in a panel on the right. Console panel with live UART communication in bottom right corner."
    caption="A screenshot of Candle Grbl controller software"
>}}

## Limit switches
I have added limit switches to my machine. This thread with
[information about the connectors on the Woodpecker board](https://github.com/gnea/grbl/issues/123)
proved to be very useful.

Grbl wiki has a page about
[wiring limit switches](https://github.com/gnea/grbl/wiki/Wiring-Limit-Switches),
so I will not go into detail about that here.

{{< todo >}}
TODO Optoisolation board
{{< /todo >}}
