# esphome-epaper-info-display

Sharing my esphome code of my e-paper info display showing infos from Home-Assistant. I am using a 2.9in e-paper display from waveshare 
and a ESP32 flashed with esphome to display mainly weather information fetched from Home-Assistant.

![Page 1](https://raw.githubusercontent.com/azrael783/esphome-epaper-info-display/main/Page1.jpg = 50%x)

![Page 2](https://raw.githubusercontent.com/azrael783/esphome-epaper-info-display/main/Page2.jpg = 50%x)

# Setup
Before you flash the controller you should check that you have all necessary sensors setup in Home-Assistant. For the sunset and sunrise info I've pulled the timestamp into a separate sensor:

```yml
# Example sensor.yaml
  - platform: template
    sensors:
      esphome_next_sunrise:
        friendly_name: "Esphome Next Sunrise"
        value_template: "{{ as_timestamp(states.sun.sun.attributes.next_dawn) | timestamp_custom ('%H:%M') }}"
      esphome_next_sunset:
        friendly_name: "Esphome Next Sunset"
        value_template: "{{ as_timestamp(states.sun.sun.attributes.next_setting) | timestamp_custom ('%H:%M') }}"
```
And for the weather warnings if also pulled some information out of the [DWD](https://www.home-assistant.io/integrations/dwd_weather_warnings/) sensor: 

```yml
      act_weather_warning_level:
        friendly_name: "Aktuelle Warnstufe"
        value_template: >-
          {% if is_state('sensor.wetterwarnung_marburg_current_warning_level', '0') %}
            0
          {% else %}
            {{ state_attr('sensor.wetterwarnung_marburg_current_warning_level', 'warning_1_level') }}
          {% endif %}
        icon_template: mdi:cloud-alert
      act_weather_warning_headline:
        friendly_name: "Aktuelle Kurzmeldung"
        value_template: >-
          {% if is_state('sensor.wetterwarnung_your-city_current_warning_level', '0') %}
            Es liegen keine Meldungen vor
          {% else %}
            {{ state_attr('sensor.wetterwarnung_your-city_current_warning_level', 'warning_1_headline') }}
          {% endif %}
        icon_template: mdi:comment-alert
```

Now download the files, put the `esp32-display.yaml` and the folder `fonts` into your `/config` folder from esphome, adapt the board info and the pin numbering to your board, flash the ESP after compiling the firmware, connect the display and be happy.
