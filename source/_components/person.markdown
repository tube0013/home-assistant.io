---
layout: page
title: "Person"
description: "Instructions on how to set up people tracking within Home Assistant."
date: 2019-02-05 22:36
sidebar: true
comments: false
sharing: true
footer: true
logo: home-assistant.png
ha_category:
  - Presence Detection
ha_qa_scale: internal
ha_release: 0.88
---

The person component allows connecting [device tracker](https://www.home-assistant.io/components/device_tracker/) entities to one or more person entities. The state updates of a connected device tracker will set the state of the person. When multiple device trackers are used, the state of person will be determined in this order:

1. If there are stationary trackers (non-GPS trackers, i.e., a router or Bluetooth 'device_trackers') presenting the status 'home', the tracker most recently updated will be used.
2. If there are trackers of type 'gps', then the most recently updated tracker will be used.
3. Otherwise, the latest tracker with status 'not_home' will be used.

Let's say, for example, that you have 3 trackers: 'tracker_gps', 'tracker_router' and 'tracker_ble'.

1. You're at home, all 3 devices show status 'home' - status of your Person entity will be 'home' with source 'tracker_router' or 'tracker_ble', whichever was most recently updated.
2. You just left home. 'tracker_gps' shows status 'not_home', but the other two trackers show status 'home' (they may not have yet updated due to their 'consider_home' setting see [device_tracker](https://www.home-assistant.io/components/device_tracker/#configuring-a-device_tracker-platform)). Since the stationary trackers have priority, you are considered 'home'.
3. After some time, both stationary trackers show status 'not_home'. Now your Person entity has status 'not_home' with source 'tracker_gps'.
4. While you are away from home, your Home Assistant is restarted. Until 'tracker_gps' receives an update, your status will be determined by the stationary trackers, since they will have the most recent update after a restart. Obviously, the status will be 'not_home'.
5. Then you're going into a zone you have defined as 'zone1', 'tracker_gps' sends an update, and now your status is 'zone1' with source 'tracker_gps'.
6. You've returned home and your mobile device has connected to the router, but 'tracker_gps' hasn't updated yet. Your status will be 'home' with source 'tracker_router'.
7. After the 'tracker_gps' update occurs, your status will still be 'home' with source 'tracker_router' or 'tracker_ble', whichever has the most recent update.

TL;DR: When you're at home, your position is determined first by stationary trackers (if any) and then by GPS. When you're outside your home, your position is determined firstly by GPS and then by stationary trackers.

**Hint**: When you use multiple device trackers together, especially stationary and GPS trackers, it's advisable to set `consider_home` for stationary trackers as low as possible see [device_tracker](https://www.home-assistant.io/components/device_tracker/#configuring-a-device_tracker-platform)).

You can manage persons via the UI from the person page inside the configuration panel or via `YAML` in your `configuration.yaml` file.

## {% linkable_title Configuring the `person` component via the Home Assistant configuration panel %}

If you prefer to use the configuration panel to configure the `person` component simply add one line to your `configuration.yaml` file and restart Home Assistant.

```yaml
person:
```

## {% linkable_title Configuring the `person` component via YAML %}

If you prefer YAML, you can also configure your persons via `configuration.yaml`:

```yaml
# Example configuration.yaml entry
person:
  - name: Ada
    id: ada6789
    device_trackers:
      - device_tracker.ada
```

{% configuration %}
  id:
    description: A unique id of the person.
    required: true
    type: string
  name:
    description: The name of the person.
    required: true
    type: string
  user_id:
    description: The user id of the Home Assistant user account for the person. *`user_id` (aka `ID`) of users can be inspected in the "Users"/"Manage users" screen in the configuration panel.*
    required: false
    type: string
  device_trackers:
    description: A list of device tracker entity ids to track. These will represent the state of the person.
    required: false
    type: list, string
{% endconfiguration %}

An extended example would look like the following sample:

```yaml
# Example configuration.yaml entry
person:
  - name: Ada
    id: ada6789
    device_trackers:
      - device_tracker.ada
  - name: Stacey
    id: stacey12345
    user_id: 12345678912345678912345678912345
    device_trackers:
      - device_tracker.stacey
      - device_tracker.beacon
```
