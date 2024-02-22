---
draft: false
date: 2024-02-22T06:59:55+01:00
title: "Tracking Arriva Buses in Home Assistant"
description: >-
featured_image: "map.png"
tags:
  - Home Assistant
  - AppDaemon
categories:
  - home automation
series:
---
{{< figure src="map.png"
    caption="Home Assistant map showing Arriva bus positions"
>}}

I wanted to show the [positions of Arriva buses in Plzeň
Region](https://pvvd.idpk.cz/) on my Home Assistant map.

Tracking multiple buses using the [RESTful
integration](https://www.home-assistant.io/integrations/rest/) was somewhat
painful due to difficulties with selecting a subset of an array using
JSONPath, so I decided to use
[AppDaemon](https://appdaemon.readthedocs.io/en/latest/) instead.

The configuration consists of a [configuration
package](https://www.home-assistant.io/docs/configuration/packages/) and an
AppDaemon app:  
`packages/bus.yaml`:
```yaml
recorder:
  exclude:
    entities:
      # The update automation changes attributes frequently
      - automation.update_arriva_bus_positions
    entity_globs:
      # Do not record bus position history
      - sensor.arriva_bus_*

input_button:
  # press this button to update bus positions
  arriva_bus_update:
    name: Update arriva bus positions
    icon: "mdi:update"

# see appdaemon app

automation:
  # update bus positions every 5 minutes
  - alias: Update arriva bus positions
    id: update_arriva_bus_positions
    trigger:
      - platform: time_pattern
        minutes: /5
    action:
      - service: input_button.press
        target:
          entity_id: input_button.arriva_bus_update
```

`appdaemon/apps/arriva.py`:
```python3
import hassapi as hass
import requests
import json


API_URL: str = "https://pvvd.idpk.cz/Ajax/GetPoints"

BUSES_TO_TRACK = (
    "460redacted",
)

DEVICE_TRACKER_PREFIX = "arriva_bus_"

UPDATE_ENTITY = "input_button.arriva_bus_update"


class ArrivaBus(hass.Hass):
    def initialize(self):
        self.tracked_ids = set()
        self.listen_state(self.state_changed, UPDATE_ENTITY)

    def state_changed(self, entity, attribute, old, new, kwargs):
        self.updateState(None)

    def updateState(self, kwargs):
        # TODO verify=False, because the server does not send a valid
        # certificate chain
        res = requests.get(API_URL, verify=False)

        if res.status_code != 200:
            raise Exception(f"Failed request: {res}")

        res_json = json.loads(res.content)

        tracked_buses = [bus for bus in res_json if bus["text"] in BUSES_TO_TRACK]

        # move old trackers to coordinates 0,0
        bus_ids = { bus["id"] for bus in tracked_buses }
        for extra_bus in (self.tracked_ids - bus_ids):
            self.call_service(
                "device_tracker/see",
                dev_id=f"{DEVICE_TRACKER_PREFIX}{extra_bus}",
                gps=[0, 0],
                attributes={
                    "valid": False,
                }
            )
        self.tracked_ids = bus_ids

        # update trackers
        for bus in tracked_buses:
            self.call_service(
                "device_tracker/see",
                dev_id=f"{DEVICE_TRACKER_PREFIX}{bus['id']}",
                gps=[bus["lat"], bus["lng"]],
                attributes={
                    "valid": True,
                    "text": bus["text"],
                    "finalStopName": bus["finalStopName"],
                    "delay": bus["delay"],
                }
            )
```

`appdaemon/apps/apps.yaml`:
```yaml
arriva:
  module: arriva
  class: ArrivaBus
```

All buses matching `BUSES_TO_TRACK` are added to Home Assistant as
`device_tracker` entities with useful attributes like `delay` and
`finalStopName`.

To show a map with all buses (but no other device trackers) in a dashboard,
we will need a custom card called
[auto-entities](https://github.com/thomasloven/lovelace-auto-entities) to
build the list of entities dynamically:

```yaml
type: custom:auto-entities
card:
  type: map
  title: Arriva bus positions
  hours_to_show: 0
card_param: entities
filter:
  include:
    - entity_id: device_tracker.arriva_bus_*
```
