# Generic Hygrostat
The `generic_hygrostat` climate platform is a hygrostat implemented in Home Assistant. It uses a sensor and a switch connected to a dryer or air humidifier under the hood. When in dryer mode, if the measured humidity is above than the target humidity, the dryer will be turned on and turned off when the required humidity is reached. When in air moist mode, if the measured humidity is below than the target humidity, the air humidifier will be turned on and turned off when required humidity is reached. One Generic Hygrostat entity can only control one switch. If you need to activate two switches, one for a dryer and one for an air humidifier, you will need two Generic Hygrostat entities.

```yaml
# Example configuration.yaml entry
climate:
  - platform: generic_hygrostat
    name: Bathroom
    dryer: switch.bathroom_fan
    target_sensor: sensor.bathroom_humidity
```

## Installation instructions

Copy the contents of `custom_components/generic_hygrostat/` to `<your_config_dir>/custom_components/generic_hygrostat/` and restart Home Assistant.

## Configuration variables

**name** *(string)(Required)*  
Name of hygrostat.  
*Default value:* Generic Hygrostat  

**dryer** *(string)(Required)*  
`entity_id` for dryer switch, must be a toggle device. Becomes air humidifier switch when `moist_mode` is set to true.  

**target_sensor** *(string)(Required)*  
`entity_id` for a humidity sensor, target_sensor.state must be humidity.  

**min_humidity** *(float)(Optional)*  
Set minimum set point available.  
*Default value:* 30  

**max_humidity** *(float)(Optional)*  
Set maximum set point available.  
*Default value:* 99  

**target_humidity** *(float)(Optional)*  
Set initial target humidity. Failure to set this variable will result in target humidity being set to null on startup. As of version 0.59, it will retain the target humidity set before restart if available.  

**moist_mode** *(boolean)(Optional)*  
Set the switch specified in the **dryer** option to be treated as a humidifying device instead of a drying device.  
*Default value:* false  

**min_cycle_duration** *(time | integer)(Optional)*  
Set a minimum amount of time that the switch specified in the **dryer** option must be in its current state prior to being switched either off or on.  

**dry_tolerance** *(float)(Optional)*  
Set a minimum amount of difference between the humidity read by the sensor specified in the **target_sensor** option and the target humidity that must change prior to being switched on. For example, if the target humidity is 50 and the tolerance is 5 the dryer will start when the sensor equals or goes above 55.  
*Default value:* 3  

**moist_tolerance** *(float)(Optional)*  
Set a minimum amount of difference between the humidity read by the sensor specified in the **target_sensor** option and the target humidity that must change prior to being switched off. For example, if the target humidity is 50 and the tolerance is 5 the dryer will stop when the sensor equals or goes below 45.  
*Default value:* 3  

**keep_alive** *(time | integer)(Optional)*  
Set a keep-alive interval. If set, the switch specified in the **dryer** option will be triggered every time the interval elapses. Use with dryers and air humidifiers that shut off if they don’t receive a signal from their remote for a while. Use also with switches that might lose state. The keep-alive call is done with the current valid climate integration state (either on or off).  

**initial_hvac_mode** *(string)(Optional)*  
Set the initial HVAC mode. Valid values are `off` or `dry`. Value has to be double quoted. If this parameter is not set, it is preferable to set a **keep_alive** value. This is helpful to align any discrepancies between **generic_hygrostat** and **dryer** state.  

Time for `min_cycle_duration` and `keep_alive` must be set as "hh:mm:ss" or it must contain at least one of the following entries: `days:`, `hours:`, `minutes:`, `seconds:` or `milliseconds:`. Alternatively, it can be an integer that represents time in seconds.

Currently the `generic_hygrostat` climate platform supports 'dry' and 'off' hvac modes. You can force your `generic_hygrostat` to avoid starting by setting HVAC mode to 'off'.

## Full configuration example

```yaml
climate:
  - platform: generic_hygrostat
    name: Bathroom
    dryer: switch.bathroom_fan
    target_sensor: sensor.bathroom_humidity
    min_humidity: 35
    max_humidity: 65
    moist_mode: false
    target_humidity: 50
    dry_tolerance: 0
    moist_tolerance: 5
    min_cycle_duration:
      seconds: 5
    keep_alive:
      minutes: 3
    initial_hvac_mode: "off"
```

## Known bugs

- Graph display does not work. The reason may be in one of the following files:
  - [home-assistant-polymer/src/components/state-history-chart-line.js](https://github.com/home-assistant/home-assistant-polymer/blob/dev/src/components/state-history-chart-line.js)
  - [home-assistant-polymer/src/panels/lovelace/cards/hui-thermostat-card.ts](https://github.com/home-assistant/home-assistant-polymer/blob/dev/src/panels/lovelace/cards/hui-thermostat-card.ts)
  - [home-assistant-polymer/src/data/history.ts](https://github.com/home-assistant/home-assistant-polymer/blob/dev/src/data/history.ts)
- No [HVAC action](https://developers.home-assistant.io/docs/en/entity_climate.html#hvac-action) for moist mode (need HVAC_MODE_MOIST).
- Missing [temperature_unit()](https://github.com/avdeevsv91/ha_generic_hygrostat/blob/master/custom_components/generic_hygrostat/climate.py#L219) causes an [NotImplementedError](https://github.com/home-assistant/home-assistant/blob/dev/homeassistant/components/climate/__init__.py#L265). In fact, this is not necessary, the temperature is not used.

If you can fix it, please do it!
