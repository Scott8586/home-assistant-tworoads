## Notee for Tworoads Home Assistant Install

### June 3rd, 2019 - Bayesian Sensor for Presence

Have been trying out three different trackers, owntracks via MQTT, with broker on hub, separate internet facing computer:
With a SSL/port 8883 based connection on the external interface, and a port 1883 based connection on the VPN interface.

It works, each sensor has different characteristics.  iOS is more consistent, but less sensitve than owntracks.  Owntracks
seems a little more jumpy.

Unfi based device tracker detects home very quickly, but away takes much longer.  Attempting to adjust this with

```
  detection_time: 60
```

Which is specific to the unifi platform.

Bayesian sensor for detecting presense at home looks like this:

```
- platform: bayesian
  prior: 0.36
  name: 'Scott Presence'
  probability_threshold: 0.9
  observations:
    - entity_id: 'device_tracker.scotts_iphone'
      prob_given_true: 0.8
      prob_given_false: 0.1
      platform: 'state'
      to_state: 'home'
    - entity_id: 'device_tracker.srp_iphone'
      prob_given_true: 0.9
      prob_given_false: 0.2
      platform: 'state'
      to_state: 'home'
    - entity_id: 'device_tracker.srp_owntracks'
      prob_given_true: 0.9
      prob_given_false: 0.4
      platform: 'state'
      to_state: 'home'
```

### March 2nd, 2021

1. Moved most of the automations to single task files, added leading letters:

  - C: Command
  - L: Location update/changes
  - N: Notifications
  - S: System Control
  - Z: Zone Changes

2. Added Vera Mode control code (copied from HorizonView).

3. Removed Bayesian sensor for presense - just use normal person thing

4. Added car Icons to car tracking stuff, via known_devices.yaml - may need to change this?

5. Removed superflous secrets and traivs secrets

6. Installed a UCKG2+ last night so:

  - Updated UniFi controller host
  - Moved to using Unifi Protect, reinstalled cameras

