---
draft: false
date: 2025-10-27T07:59:17+01:00
title: "Gophert NPS-1601 Power Supply Repair"
description: >-
  Repair notes and partial reverse engineered schematic for Gophert NPS-1601
  switch mode power supply.
featured_image: "NPS-1601.jpg"
tags:
categories:
  - electronics
series:
---
My friend needed to charge a few LiFePO4 cells in a solar system to fix an
imbalance that prevented the controller from charging the whole bank properly.
He took his NPS-1601 power supply, and because he needed more than 30 V, he
also borrowed mine and connected them in series. He then used them to charge the
batteries (first in constant current mode, later automatically switching to
constant voltage). He did this for several batteries in the bank, and for the
last one, he accidentally connected the battery backwards.

{{< figure src="NPS-1601.jpg" >}}

## The Aftermath
Upon opening the power supplies, there were several obvious failures (pretty
much the same in both supplies):
- two electrolytic capacitors had exploded from the reverse polarity,
- the current shunt had melted off and split into several pieces,
- the mains fuse was blown.

## Schematic
I haven't reverse engineered the whole thing, but I am pretty sure the device
actually contains two power supplies. One of them is the high-power supply
with adjustable voltage and current based on a TL494 IC, and the other one
provides roughly 12V to power the display and the TL494.

The control loop for the TL494 supply is fully analog. The microcontroller on
the display board just generates reference voltages for the opamps. It also
uses ADCs to measure the output voltage and current, but it only uses the
measured values to update the displays. As far as I can tell, it does not
implement any feedback loop of its own.

{{< figure src="NPS-1601-reverse engineered-schematic.svg"
    caption="Partial reverese-engineered schematic of NPS-1601 control loop"
>}}

I didn't pay much attention to the primary side, but it should be pretty
similar to a standard TL494 power supply like these [AT and ATX PC power
supplies schematics](https://danyk.cz/s_atx_en.html), except it does not need
the self-starting functionality.


## Repair
For testing, I have connected a light bulb across the blown fuse.
The light bulb lit up brightly, indicating there was likely a short on the
primary side. Both switching MOSFETs have failed short.
One of the failed units had SLF13N50C transistors, while the other had
F13NK50Z. I replaced all 4 with WML15N50D1B. I also replaced the exploded
electrolytic caps and the current shunt (more on that later).

After flipping the power switch, the display lit up, and the output was able
to regulate to the set voltage without trouble. However, output current
measurement wasn't working, and neither was the regulation. When I shorted the
output, the light bulb lit up again, even though I had the supply set to 12V
0.1A.

It turns out some 0603 resistors in the feedback loop (in particular R91 and
R96) have increased in resistance from 75R and 220R respectively to ~10k.
After replacing these, constant current (CC) mode started working correctly.

{{< figure src="repair.jpg"
    caption="NPS-1601 with resistance decade boxes replacing the faulty resistors, a light bulb connected in place of the fuse, and a rheostat on the output"
>}}


### Note on current shunt replacement
I didn't know the resistance of the original current shunt. Based on some
4-wire measurements on the pieces that were left after it melted, I guessed 10
to 20 mΩ.

I replaced it with a 15 mΩ shunt (CSR-1.8-R015), but when I later went
to calibrate the supply, it indicated 5A when the actual current was about
6.3A. The adjustment range is large enough to fix this in software, but it
means the original shunt was likely closer to 20 mΩ.

The CSR-1.8-R015 has the correct lead pitch and total wire lenght, but the
shape (and cross section) is different than the original. It is too tall,
interfering with the bottom cover of the supply. I had to bend it to something
resembling the original shape.

{{< figure src="shunt.jpg" width="300"
    caption="CSR-1.8-R015 current shunt modified to the proper shape"
>}}


## Calibration
This [calibration
procedure](https://www.bitsammler.de/downloads/Manuals/CPS-3205C%20Netzteil/calibration.pdf)
allowed me to get the current measurement and regulation back in spec.

## BOM
Components needed to repair one unit (with this specific fault):

| MPN                 | Description                                                         | Qty |
| ------------------- | --------------------------------------------------------------------| ---:|
| EGF477M1VG16RRTHP-R | Capacitor: electrolytic; low ESR; THT; 470uF; 35VDC; Ø10x16mm; ±20% |   1 |
| EGF107M1VE11RRSHP   | Capacitor: electrolytic; low ESR; THT; 100uF; 35VDC; Ø6.3x11mm      |   1 |
| WML15N50D1B         | Transistor: N-MOSFET; WMOS™ D1; unipolar; 500V; 15A; Idm: 60A; 45W  |   2 |
| CSR-1.8-R015*       | Resistor: wire-wound; sensing,precise; THT; 15mΩ; ±5%; 11A; radial  |   1 |
| 121.000G            | Fuse holder; 5x20mm; 6.3A; Plating: silver plated; Contacts: brass  |   2 |
| 0216002.HXP         | Fuse: fuse; 2A; 250VAC; quick blow; ceramic,cylindrical; 5x20mm     |   1 |
| 0603SAF2200T5E      | Resistor: thick film; SMD; 0603; 220Ω; 0.1W; ±1%; 50V; -55÷155°C    |   1 |
| 0603SAF750JT5E      | Resistor: thick film; SMD; 0603; 75Ω; 0.1W; ±1%; 50V; -55÷155°C     |   1 |

\* you might want to look for a 20mΩ resistor/shunt with the same physical
dimensions instead.
