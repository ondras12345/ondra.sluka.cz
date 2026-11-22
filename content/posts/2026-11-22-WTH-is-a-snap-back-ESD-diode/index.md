---
draft: false
date: 2026-11-22T13:20:53+01:00
title: "WTH is a Snap-Back ESD Diode?"
description: |-
  My favorite ESD5Z3.3T1G unidirectional TVS diode was not in stock,
  so I had to purchase ESD3V3WT instead. I'm only now discovering what it is
  that I had bought...

  An I-V curve tracer shows that the device does not conduct when forward
  biased (tested up to 20V).
featured_image: "IV-ESD3V3WT.png"
tags:
categories:
  - electronics
series:
---
My favorite `ESD5Z3.3T1G` unidirectional TVS was not in stock when I was
purchasing components for the latest project. No worries, I thought, I will
just use the parametric search to find a suitable replacement.
The only available unidirectional TVS in the same package was the `ESD3V3WT`.
I had a quick look at the datasheet. Yes, the peak power rating is lower, and
the breakdown characteristics are a bit different, but that shouldn't be a
problem in my application.

When I went to assemble my board, I used the diode test functionality on my
DMM as usual, and nothing! The diodes wouldn't conduct in the forward
direction.

Let's compare the datasheets
([`ESD5Z3.3T1G`](https://diotec.com/request/datasheet/esd5z2v5.pdf) on the left,
[`ESD3V3WT`](https://diotec.com/request/datasheet/esd3v3wt.pdf) on the right):
{{< figure src="datasheets.svg" caption="Comparison of Diotec ESD5Z3.3T1G and ESD3V3WT datasheets with the key differences highlighted" >}}

The first pages look almost identical (most notably, the **schematic symbols
are exactly the same**), except for the Features list (highlighted in the
picture). The real difference is on page 2. Turns out the `ESD3V3WT`'s
datasheet leaves forward-bias characteristics entirely unspecified!

(The datasheet itself does not mention the word "unidirectional", but the
[diotec product page](https://diotec.com/en/product/ESD3V3WT.html) calls it a
unidiractional ESD protection diode.)

So, what is the forward-biased behavior?
I set up the classic Octopus curve tracer circuit to measure the I-V curve.
{{< figure src="octopus-curve-tracer.svg" caption="Octopus curve tracer circuit diagram" width="100%" >}}

{{< figure src="IV-ESD3V3WT.png" caption="Measured I-V curve of ESD3V3WT" >}}
I tested up to +20V in the forward direction and it didn't start
conducting!

Also, I don't see any significant snap-back in the reverse direction, but
that's probably due to the low test current (500 µA).

So yeah, I guess unidirectional in this case means "we don't know what it does
in the other direction". Obviously they didn't intend it to be a diode in the
forward direction, but then why use the schematic symbol they did, and what
good is it if they don't at least specify the maximum allowed forward voltage?

If you are interested in the semiconductor structure of a snap-back
diode (it is a multi-junction device), see
https://blog.semtech.com/tvs-its-just-a-diode-right-part-two.
