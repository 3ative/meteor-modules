# Controlling "Meteor" Modules, Timings and adding "Effects"

### Link(s) and Full ESPHome (D1 Mini) Code:
- AilExpress Link for the [Modules](https://s.click.aliexpress.com/e/_DDYmi2r) Modules used: "Blue 58mm"
- [YouTube Tutorial](https://youtu.be/8JjdSDOBXRk)
- Copy & Paste in to your Device - Enjoy 💖
```yaml
substitutions:
  name: Meteor # For Component Names - Capitals allowed

esphome:
  name: light-meteors

esp8266:
  board: d1_mini

logger:
  logs:
    ## Stop Spamming the Logs
    component: none
    switch: NONE
api:
ota:
  platform: esphome

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password

number:
# "Sliders" Used for Timing and Fine Tuning
  - platform: template
    name: $name Timing Delay
    id: main_delay
    min_value: 200
    max_value: 2000
    step: 50
    optimistic: True
    initial_value: 800

  - platform: template
    name: $name Timing Ofset
    id: main_ofset
    min_value: 0
    max_value: 200
    step: 10
    optimistic: True
    initial_value: 0

switch:
# Switches (Templated) For the "Effects" On/Off
  - platform: template
    name: $name Set1 Synced
    id: meteor_sync
    optimistic: true
    on_turn_on:
      - script.execute: synced
    on_turn_off:
      - script.stop: synced      # Stop the "synced" Script
      - script.execute: all_off  # Turn the Modules' GPIOs off

  - platform: template
    name: $name Set2 Cascade
    id: meteor_casc
    optimistic: true
    on_turn_on:
      - script.execute: casc
    on_turn_off:
      - script.stop: casc       # Stop the "cacs" Script
      - script.execute: all_off # Turn the Modules' GPIOs  off

# Switches (GPIOs) For the Meteor Modules
  - platform: gpio
    id: meteor1
    pin: D5
  - platform: gpio
    id: meteor2
    pin: D6
  - platform: gpio
    id: meteor3
    pin: D7

script:
# "synced" Turns all Modules On/Off at the same time
  - id: synced
    then:
      - while:
          condition:
            switch.is_on: meteor_sync
          then:
            - switch.turn_on: meteor1
            - switch.turn_on: meteor2
            - switch.turn_on: meteor3
            
            - delay: !lambda  return id(main_delay).state - id(main_ofset).state;
            
            - switch.turn_off: meteor1
            - switch.turn_off: meteor2
            - switch.turn_off: meteor3

# "casc" Turns Modules On/Off in Sequence: 1-2-3
  - id: casc
    then:
      - while:
          condition:
            switch.is_on: meteor_casc
          then:
            - switch.turn_on: meteor1
            - delay: !lambda  return id(main_delay).state - id(main_ofset).state;
            - switch.turn_off: meteor1
            
            - switch.turn_on: meteor2
            - delay: !lambda  return id(main_delay).state - id(main_ofset).state;
            - switch.turn_off: meteor2
            
            - switch.turn_on: meteor3
            - delay: !lambda  return id(main_delay).state - id(main_ofset).state;
            - switch.turn_off: meteor3

# "all_off" Turns Off all (swtiches) for the Modules
  - id: all_off
    then:
      - switch.turn_off: meteor1
      - switch.turn_off: meteor2
      - switch.turn_off: meteor3
```

---

<div align="center">

### 💖 Support This Project

Found this useful? Want to say thanks and fuel future creations?

**Your support keeps the creativity flowing!** 🍺✨

<table>
  <tr>
    <td align="center">
      <a href="https://www.buymeacoffee.com/3ative">
        <img src="https://img.shields.io/badge/Buy%20Me%20A%20Coffee-Support-yellow.svg?style=for-the-badge&logo=buy-me-a-coffee&logoColor=white" alt="Buy Me A Coffee"/>
        <br/>
        <b>☕ Buy me a Coffee</b>
      </a>
    </td>
    <td align="center">
      <a href="https://www.patreon.com/3ative">
        <img src="https://img.shields.io/badge/Patreon-Become%20a%20Patron-red.svg?style=for-the-badge&logo=patreon&logoColor=white" alt="Patreon"/>
        <br/>
        <b>💖 Join on Patreon</b>
      </a>
    </td>
  </tr>
</table>

**Every contribution helps bring more awesome projects to life!** 🚀

</div>

---
