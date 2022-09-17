---
draft: false
title: "KKmoon FY6900-100M Function Generator"
date: 2022-09-10T16:12:01+02:00
description: "2 channel 100&nbsp;MHz DDS arbitrary waveform generator"
featured_image: "FY6900.jpg"
tags: ["function generator"]
categories: ["electronics"]
---
I have recently (2020-08-07) bought a new function generator for my
electronics lab. It is a cheap(-ish) Chinese 2 channel 100&nbsp;MHz DDS
arbitrary waveform generator.

The KKmoon brand seems to be just an OEM by Feeltech.

{{< figure-resize src="FY6900.jpg" width="100%" resize="800x800 q75"
    alt="FY6900 Function generator"
    caption="FY6900 Function generator set to generate slowly rotating Lissajous patterns on an oscilloscope in XY mode"
>}}

{{< figure-resize src="FY6900-Lissajous.jpg" width="100%" resize="800x800 q75"
    caption="Lissajous patterns on my [Hitachi V-1565 analog oscilloscope](/equipment/hitachi-v-1565-analog-oscilloscope/)"
    alt="3:1 sine wave Lissajous pattern on an analog oscilloscope screen"
>}}


## Some useful links
- [FY6800 review and tutorial](https://www.youtube.com/watch?v=S9usQLFKZLk)
  (different model, but the UI is similar)
- [Manufacturer's website?](http://en.feeltech.net/index.php?case=archive&act=show&aid=65)
- [User guide, software, communication protocol specs](http://en.feeltech.net/index.php?case=archive&act=show&aid=64)
- [External 10MHz reference mod](https://odysee.com/@tonyalbus:bd/062-feeltech-fy6900-external-reference:a)
- [Python library](https://github.com/mattwach/fygen) (not tested yet)
- Videos by SDG electronics:
  [teardown](https://odysee.com/@sdgelectronics:0/sdg-mailbag-029-feeltech-fy6900:7),
  [PSU mod](https://odysee.com/@sdgelectronics:0/sdg-081-fy6900-signal-generator-power:1)

Unfortunately, it does not have a frequency reference input, but given that I
currently have no precision clocks nor a GPS disciplined oscillator, this does
not trouble me too much. There is a way to modify the instrument to add this
capability, see the links above.

I will try update this page each time I find something interesting.
