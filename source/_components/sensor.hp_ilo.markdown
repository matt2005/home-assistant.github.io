---
layout: page
title: "HP ILO"
description: "How to integrate HP ILO (Integrated Lights-Out) sensors within Home Assistant."
date: 2016-08-15 19:00
sidebar: true
comments: false
sharing: true
footer: true
logo: hewlett_packard_enterprise.png
ha_category: System Monitor
ha_release: 0.27
ha_iot_class: "Local Polling"
---

The `hp_ilo` platform allows you to do an API call to the HP ILO (Integrated Lights-Out) sensor of your server, and use this data in Home Assistant sensors.

If the ILO or specified jsonpath query returns only a single value (e.g. a temperature or state), it will be put in the state field. If a data structure is returned, it will be placed in the `ilo_data` attribute.

Some more details about what can be retrieved from these sensors is available in the [python-hpilo documentation](http://pythonhosted.org/python-hpilo/).

<p class='img'>
  <img src='{{site_root}}/images/screenshots/hp_ilo.png' />
</p>


To use this component in your installation, add the following to your `configuration.yaml` file:

```yaml
# Example configuration.yaml entry
sensor:
  - platform: hp_ilo
    host: IP_ADDRESS or HOSTNAME
    username: USERNAME
    password: PASSWORD
    monitored_variables:
      - name: SENSOR NAME
        sensor_type: SENSOR TYPE
```

Configuration variables:

- **host** (*Required*): The hostname or IP address on which the ILO can be reached.
- **port** (*Optional*): The port on which the ILO can be reached, defaults to port `443`.
- **username** (*Required*): The username used to connect to the ILO.
- **password** (*Required*): The password used to connect to the ILO.
- **monitored_variables** array (*Optional*): Sensors created from the ILO data. Defaults to an empty list (no sensors are created).
  - **name** (*Required*): The sensor name.
  - **sensor_type** (*Required*): The sensor type, has to be one of the specified valid sensor types.
  - **unit_of_measurement** (*Optional*): The sensors' unit of measurement.
  - **value_template** (*Optional*): When a Jinja2 template is specified here, the created sensor will output the template result. The ILO response can be referenced with the `ilo_data` variable.

Valid sensor_types:
- **server_name**: Get the name of the server this iLO is managing.
- **server_fqdn**: Get the fqdn of the server this iLO is managing.
- **server_host_data**: Get SMBIOS records that describe the host.
- **server_oa_info**: Get information about the Onboard Administrator of the enclosing chassis.
- **server_power_status**: Whether the server is powered on or not.
- **server_power_readings**: Get current, min, max and average power readings.
- **server_power_on_time**: How many minutes ago has the server been powered on.
- **server_asset_tag**: Gets the server asset tag.
- **server_uid_status**: Get the status of the UID light.
- **server_health**: Get server health information.
- **network_settings**: Get the iLO network settings.

### Example

In order to get two sensors reporting CPU fan speed and Ambient Inlet Temperature, as well as a dump of `server_health` on a HP Microserver Gen8, you could use the following in your `configuration.yaml` file

```yaml
sensor:
  - platform: hp_ilo
    host: IP_ADDRESS or HOSTNAME
    username: USERNAME
    password: PASSWORD
    monitored_variables:
      - name: CPU fanspeed
        sensor_type: server_health
        unit_of_measurement: '%'
        value_template: '{{ ilo_data.fans["Fan 1"].speed[0] }}'
      - name: Inlet temperature
        sensor_type: server_health
        unit_of_measurement: '°C'
        value_template: '{{ ilo_data.temperature["01-Inlet Ambient"].currentreading[0] }}'
      - name: Server Health
        sensor_type: server_health

```

<p class='img'>
  <img src='{{site_root}}/images/screenshots/hp_ilo_sensors.png' />
</p>

## {% linkable_title Hardware specifics %}

<p class='note warning'>
Not every hardware supports all values.
</p>

### {% linkable_title HP Microserver Gen8 %}

On this hardware you should avoid using the following sensor_types as `monitored_variables:` to prevent errors.

- `server_oa_info`
- `server_power_readings`
- `server_power_on_time`
