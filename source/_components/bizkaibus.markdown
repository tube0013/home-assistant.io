---
layout: page
title: "Bizkaibus next bus tracking sensor"
description: "Instructions on how to integrate timetable data for traveling on Bizkaibus within Home Assistant."
date: 2019-04-22 14:00
sidebar: true
comments: false
sharing: true
footer: true
logo: bizkaibus.png
ha_category: 
  - Transport
  - Sensor
ha_iot_class: Cloud Polling
ha_release: 0.93
---

The `bizkaibus` sensor will give you the time until the next bus in the selected stop.

The next website can help to determine the id of your bus stop. You can check if this is correct by going to [next link](http://apli.bizkaia.net/APPS/DANOK/TQ/DATOS_PARADAS/DATOS_Paradas.xml) and look the PR_CODE for the STOP_ID.

For a correct use of the sensor the selected route must stop in the selected stop.

Then add the data to your `configuration.yaml` file as shown in the example:

```yaml
# Example configuration.yaml entry
sensor:
  - platform: bizkaibus
    stopid: STOP_ID
    route: ROUTE_ID
```

{% configuration %}
stopid:
  description: The ID of the bus stop to get the information for.
  required: true
  type: string
route:
  description: The ID of the bus route to get information for. This is the same as the bus number, e.g., `A3641`.
  required: true
  type: string
name:
  description: A friendly name for this sensor.
  required: false
  default: Next Bus
  type: string
{% endconfiguration %}

The public RTPI information is coming from [Bizkaibus API](http://apli.bizkaia.net/APPS/DANOK/TQWS/TQ.ASMX).
