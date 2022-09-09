---
title: "NG1620-BL Lab Bench Power Supply"
date: 2020-12-14T12:07:00+02:00
draft: false
description: "Modifications of a cheap 0&ndash;15 V 0&ndash;2 A adjustable DC linear power supply with passive cooling"
featured_image: "NG1620-BL-front-panel.jpg"
---

This is the first lab bench power supply I have ever owned.
It is a cheap 0&ndash;15 V 0&ndash;2 A adjustable DC linear power supply with
passive cooling.

There appear to be many different versions of this product, so make sure to
find the matching schematic for your unit.

## Some useful links
- [Reverse-engineered schematic](http://kiedontaa.blogspot.com/2015/09/lab-power-supply-repair-part-1.html)
- [Schematics](https://www.mikrocontroller.net/topic/377324) &mdash; multiple
  versions of the PCB
- [A random teardown video](https://www.youtube.com/watch?v=XCteGaj37r8)
  &mdash; yet another version
- [Multi-turn potentiometers modification](http://kiedontaa.blogspot.com/2015/10/multi-turn-potentiometer-modification.html)

## Modification
I have modified my unit to use multi-turn pots and added a pushbutton that
shorts the output, thus making it easy to adjust the constant current (CC)
setting.

After changing the pots, you may need to adjust the internal trimmers to be
able to reach the full range of output voltage and current.

{{< figure-resize src="NG1620-BL-internals.jpg" caption="A picture of the internals of my NG1620-BL power supply after modification" >}}

{{< figure-resize src="NG1620-BL-front-panel.jpg" caption="Front panel of my NG1620-BL power supply after modification" >}}
