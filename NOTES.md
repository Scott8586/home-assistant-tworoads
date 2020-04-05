## Notes

### Different Trackers, Bayesian Sensor for Presense
#### June 3rd, 2019 

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
