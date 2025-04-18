# Make Dumb Heater Smart with HA

## Generic Thermostat Yaml (Celsius):

```yaml
climate:
  - platform: generic_thermostat
    name: Bath Thermostat
    heater: switch.plug_5_local
    target_sensor: sensor.bath_temperature
    min_temp: 5
    max_temp: 35
    ac_mode: false
    target_temp: 5
    cold_tolerance: 0.3
    hot_tolerance: 0
    min_cycle_duration:
      seconds: 5
    keep_alive:
      minutes: 3
    initial_hvac_mode: "off"
    home_temp: 21
    comfort_temp: 25
    sleep_temp: 19
    away_temp: 5
    precision: 0.5
```

## Generic Thermostat Yaml (Fahrenheit):

```yaml
climate:
  - platform: generic_thermostat
    name: Bath Thermostat
    heater: switch.plug_4_local
    target_sensor: sensor.bath_temperature
    min_temp: 55
    max_temp: 85
    ac_mode: false
    target_temp: 68
    cold_tolerance: 1
    hot_tolerance: 1
    min_cycle_duration:
      seconds: 5
    keep_alive:
      minutes: 3
    initial_hvac_mode: "off"
    home_temp: 68
    comfort_temp: 72
    sleep_temp: 65
    away_temp: 55
    precision: 0.5
```

## Automation for smart button

```yaml
alias: Heater bath sync
description: Turn on climate and set target temp based on switch state
triggers:
  - domain: mqtt
    device_id: d16b7f4adfef62ac0d3d19733c668579
    type: action
    subtype: single
    trigger: device
    id: "on"
  - domain: mqtt
    device_id: d16b7f4adfef62ac0d3d19733c668579
    type: action
    subtype: double
    trigger: device
    id: "off"
conditions: []
actions:
  - choose:
      - conditions:
          - condition: trigger
            id: "on"
        sequence:
          - data:
              entity_id: climate.bath_thermostat
              temperature: "{{ states('sensor.bath_temperature') | float + 5 }}"
            action: climate.set_temperature
            enabled: true
          - data:
              hvac_mode: heat
            action: climate.set_hvac_mode
            target:
              entity_id: climate.bath_thermostat
      - conditions:
          - condition: trigger
            id: "off"
        sequence:
          - data:
              entity_id: climate.bath_thermostat
              hvac_mode: "off"
            action: climate.set_hvac_mode
    enabled: true

```
