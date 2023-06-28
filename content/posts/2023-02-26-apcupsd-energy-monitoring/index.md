---
draft: false
date: 2023-02-26T11:32:13+01:00
title: "Using Data from a UPS to Calculate PC Energy Consumption"
description: >-
  A home automation experiment -- trying to calculate PC power consumption
  using `apcupsd`.
featured_image: "PC-power-consumption.png"
tags:
  - Home Assistant
categories:
  - home automation
series:
---
{{< figure src="PC-power-consumption.png" width="100%"
    caption="Desktop computer power consumption graph."
>}}

`apcupsd` is a piece of software that can communicate with APC uninterruptible
power supplies (UPSs). Among other things, it reports load percentage
(`LOADPCT`) and nominal output power (`NOMPOWER`). I wanted to see if I could
use this information to monitor the load's power consumption.

## The Setup
The APC Back-UPS XS 950U is connected to my brother's Windows computer running
[`apcupsd` for Windows](http://www.apcupsd.org/). Home Assistant connects to
the `apcupsd` instance over TCP using the
[APC UPS Daemon](https://www.home-assistant.io/integrations/apcupsd/)
integration.

Because we turn the PC off at night, I needed to make sure this could be
detected in Home Assistant. It turns out the `apcupsd` integration wasn't
ready for this as it [would just spam the system log with `Connection refused`
messages instead of making its entities
`unavailable`](https://github.com/home-assistant/core/issues/85919).
I threw together a
[PR to fix this behavior](https://github.com/home-assistant/core/pull/85920)
and it got merged, so everything should work out of the box in Home Assistant
`2023.2`.

To do the actual calculations, I added the following [configuration
package](https://github.com/home-assistant/core/pull/85920):
```yaml
# vim: tabstop=2 softtabstop=2 shiftwidth=2 expandtab

sensor:
  - platform: integration
    name: UPS power integral
    source: sensor.ups_power
    unit_prefix: k
    method: left


template:
  - sensor:
      - name: UPS power
        unit_of_measurement: "W"
        state: '{{ states("sensor.ups_load")|float(0)/100 * states("sensor.ups_nominal_output_power")|int(0) }}'
        # Integral ignores state changes from / to unavailable and unknown.
        # This could theoretically result in imprecise integration if ups_load
        # was steady before going unavailable.
        # Default values in float and int filters should ensure state is zero
        # when source sensors are unavailable.
        #availability: '{{ states("sensor.ups_load") not in ["unknown", "unavailable"] }}'
        device_class: power
```

## Results
I have hooked up a kill-a-watt-style device to the UPS's input and monitored
the power consumption for about 11 days. Unfortunately, the kill-a-watt only
displays the energy reading in 100s of Wh, so I couldn't get very precise
readings.

The kill-a-watt was connected to the UPS's **input**, so it was measuring its
conversion losses and standby consumption in addition to the actual load.

I wrote down the energy shown on the kill-a-watt's display every time I turned
on / off the computer. By summing `(current_value - previous_value)` for
datapoints when the computer was turned on, I came up with a total consumption
of 12.9&nbsp;kWh. However, the individual values that make up the sum were all in
the range of 0.4&nbsp;kWh to 1.5&nbsp;kWh, so the insufficient resolution
could easily have a 10% impact.

The Riemann sum sensor in Home Assistant gave me a total value of
10.695&nbsp;kWh. That's 17&nbsp;% less than the kill-a-watt reading. This
measurement error is most likely caused by the insufficient resolution of the
kill-a-watt, as well as by the conversion losses in the UPS (which seems to
consume about 15W in standby with the load completely disconnected).

I'm sorry for being inconclusive about the accuracy of this method of
measuring PC energy consumption. I should really repeat this experiment using
a better energy meter as a reference.
