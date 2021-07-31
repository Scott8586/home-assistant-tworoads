# Notee for Tworoads Home Assistant Install

## June 3rd, 2019 - Bayesian Sensor for Presence

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

## March 2nd, 2021

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

## March 5th, 2021

Experimented with capturing snapshots of protect cameras to post most recen motion activity

Example automation for side yard snapshot using camera.snapshot().
Also used telegram_notify data/photo code to send the image with a caption.
Doorbell is connected by Wifi, and that connection is sometimes fragile, so
delay the sending of the image for 10s.

Might want to modify on whether we're home?

```

id: 'sideyardsnapshot'
alias: 'N: Side Yard Snapshot'
mode: single # Only run once at a time  
max_exceeded: silent # Hide warnings when triggered while in delay.
trigger:
  platform: state
  entity_id:
   - binary_sensor.motion_tr_side_yard
  to: 'on'
action:
#- delay: '10'
#- service: unifiprotect.save_thumbnail_image
- service: camera.snapshot
  data:
    entity_id: camera.tr_side_yard
    filename: "/config/www/snapshots/tr_side_yard.png"
#- service: !secret telegram_notify
#  data_template:
#    message: "Side Yard Motion"
- service: !secret telegram_notify
  data:
    title: ''
    message: ''
    data:
      photo:
      - file: "/config/www/snapshots/tr_side_yard.png"
        caption: "Side Yard {{now().strftime('%H:%M %d-%m-%Y')}}"

```

Then make that available as a secondary camera object through the local_file:

```

camera:
  - platform: local_file             
    name: Front Door Snapshot
    file_path: /config/www/snapshots/tr_front_door.png
  - platform: local_file                                                    
    name: Side Yard Snapshot
    file_path: /config/www/snapshots/tr_side_yard.png

```

And have lovelace present as an image using picture-elements - this causes the card to check for new 
images constantly thereby defeating any caching.  Seems to work.

```

     - type: picture-elements
       elements: []                 
       entity: binary_sensor.motion_tr_side_yard
       camera_image: camera.side_yard_snapshot


```
