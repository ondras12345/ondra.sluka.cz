---
draft: false
date: 2025-08-01T19:08:08+02:00
title: "Reverse engineered schematic of RobotDyn AC Dimmer 2 Channels"
description: >-
  Reverse engineered circuit diagram of and some notes about the RobotDyn AC
  Dimmer.
featured_image: "dimmer-top.jpg"
tags:
  - teardown
categories:
  - electronics
series:
---
I had some trouble getting my dimmer to work properly, so I decided to
completely reverse engineer the circuit.

Having done that, I have to say I wouldn't buy it again. There are some
significant safety concerns, like using SMD ceramic caps instead of proper
X2-rated capacitors in the RC snubbers. This thing is going to catch fire...

I also think the 8&nbsp;A / channel rating is nonsense. At that current, the
triac is going to dissipate almost 8&nbsp;W of power! You cannot possibly do
that with these tiny heatsinks without insane amounts of forced airflow.

{{< figure src="robotdyn-2ch-dimmer-schematic.svg"
    caption="Reverse engineered schematic of RobotDyn AC Dimmer 2 channels"
>}}
