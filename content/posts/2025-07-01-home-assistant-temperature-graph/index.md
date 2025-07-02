---
draft: false
date: 2025-07-01T15:36:16+02:00
title: "Overlaid Daily Temperature Graph for Home Assistant "
description: >-
  A guide for setting up a UI card that shows outdoor temperatures at
  each hour of the day, with multiple days overlaid on the same graph.
  Useful for comparing the forecast for tomorrow with today's / yesterdays
  weather.
featured_image: "temperature.png"
tags:
  - Home Assistant
categories:
  - home automation
series:
---
{{< figure src="temperature.png"
    caption="Overlaid daily temperature graph in Home Assistant"
>}}

This is just a quick post documenting how I set up the above graph in Home
Assistant 2025.6 using
[apexcharts-card](https://github.com/RomRider/apexcharts-card).

The graph overlays historical data from my temperature sensor with the
forecast for the next day.

First, we need to create a sensor that has the weather forecast as it's
attributes:
```yaml
template:
  - triggers:
      # every hour
      - trigger: time_pattern
        minutes: 55
      - trigger: homeassistant
        event: start
      - platform: event
        event_type: event_template_reloaded
    actions:
      - action: weather.get_forecasts
        data:
          type: hourly
        target:
          entity_id: weather.dru
        response_variable: hourly
    sensor:
      - name: Temperature forecast
        state: "{{ hourly['weather.dru'].forecast[0].temperature }}"
        attributes:
          forecast: >-
            {% set f = hourly["weather.dru"].forecast %}
            {% set keys = f | map(attribute='datetime') | list %}
            {% set values = f | map(attribute='temperature') | list %}
            {{ dict(zip(keys, values)) }}
        unit_of_measurement: °C
        device_class: temperature
        state_class: measurement
```

The attributes of `sensor.temperature_forecast` should look like this:
```yaml
state_class: measurement
forecast:
  "2025-07-01T14:00:00+00:00": 30.6
  "2025-07-01T15:00:00+00:00": 30.6
  "2025-07-01T16:00:00+00:00": 30.2
  "2025-07-01T17:00:00+00:00": 29.4
  "2025-07-01T18:00:00+00:00": 27.9
  "2025-07-01T19:00:00+00:00": 25.3
  "2025-07-01T20:00:00+00:00": 23.7
  "2025-07-01T21:00:00+00:00": 21.7
  "2025-07-01T22:00:00+00:00": 21.2
  "2025-07-01T23:00:00+00:00": 20
  "2025-07-02T00:00:00+00:00": 18.9
  "2025-07-02T01:00:00+00:00": 18
  "2025-07-02T02:00:00+00:00": 17.5
  "2025-07-02T03:00:00+00:00": 17.2
  "2025-07-02T04:00:00+00:00": 18
  "2025-07-02T05:00:00+00:00": 19.6
  "2025-07-02T06:00:00+00:00": 21.8
  "2025-07-02T07:00:00+00:00": 24.2
  "2025-07-02T08:00:00+00:00": 26.8
  "2025-07-02T09:00:00+00:00": 29.1
  "2025-07-02T10:00:00+00:00": 30.9
  "2025-07-02T11:00:00+00:00": 32
  "2025-07-02T12:00:00+00:00": 32.8
  "2025-07-02T13:00:00+00:00": 33.2
unit_of_measurement: °C
device_class: temperature
friendly_name: Temperature forecast
```

Now the chart card can be added to the UI:
```yaml
type: custom:apexcharts-card
graph_span: 1d
span:
  start: day
header:
  show: true
  title: Outdoor temperature
now:
  show: true
series:
  - entity: sensor.outsidetemp
    offset: "-4d"
    name: " "
    stroke_width: 2
    show:
      legend_value: false
  - entity: sensor.outsidetemp
    offset: "-3d"
    name: " "
    stroke_width: 3
    show:
      legend_value: false
  - entity: sensor.outsidetemp
    offset: "-2d"
    name: " "
    stroke_width: 4
    show:
      legend_value: false
  - entity: sensor.outsidetemp
    offset: "-1d"
    name: " "
    stroke_width: 5
    show:
      legend_value: false
  - entity: sensor.outsidetemp
    extend_to: now
    name: " "
    stroke_width: 7
    show:
      legend_value: false
  - entity: sensor.temperature_forecast
    name: Forecast
    stroke_width: 6
    show:
      legend_value: false
    data_generator: >-
      return Object.entries(entity.attributes.forecast).map( ([date_str, temp])
      => [new Date(date_str).getTime(), temp] );
  - entity: sensor.temperature_forecast
    name: Forecast tomorrow
    stroke_width: 4
    show:
      legend_value: false
    extend_to: false
    data_generator: >-
      return Object.entries(entity.attributes.forecast).map( ([date_str, temp])
      => [new Date(date_str).getTime() -24*3600000, temp] );
```

`sensor.outsidetemp` is my temperature sensor (source of historical data).
If you don't have one, you could use `sensor.temperature_forecast` instead.

That's it!
