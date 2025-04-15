# Lindy4x4Matrix
This project is about IP control of a Lindy 38143 4*4 HDMI matrix.
![image](https://github.com/PhillyGilly/Lindy4x4Matrix/assets/56273663/707fd973-bb74-47e3-8e80-2244d8dc810b)

The matrix enables any HDMI input (numbered 1 to 4) to be connected simultanesouly to any HDMI-over-Cat6 output (identifioed as A to D).
If for example you want to watch the Amazon Firestick in the lounge you connect 2 to A, or if you want to watch Sky+ in the kitchen you connect 1 to B.
Users have to memorize the codes for various inputs and outputs which makes the matrix completely unfriendly and not usable by people that just want to watch TV.
Further the matrix consumes power by being switched on continuously.
So the requirement is to be able to manage the matrix using a table interface (i.e. Home Assistant) or by Voice Assistant (i.e. Alexa)
I had previously tried and failed to do this in 2019 but inspired by @CameronGray's excellent video https://youtu.be/BE6ZJmede5Q?si=LtarL4RUJyJyKgxZ, I decided to revisit this project in 2023.

Lindy provide a fairly basic PC application to remotely control the matrix.
![image](https://github.com/PhillyGilly/Lindy4x4Matrix/assets/56273663/2fc655cf-bc2b-44de-bef6-8c0bbdb5a8a5)

Exactly the same PC application is also used with the Lindy 38152 matrix and a bit of fortunate googling uncovered the a document (appended) which gave command hex strings codes for that matrix's RS232 interface, and most importantly how to generate checksum codes.
I created an excel spreadsheet (appened) which calculated the complete commands and checked them against values observed using Wireshark(TM).

The command hex string codes are "fired" by Home Assistant as Shell Commands.
For speed of access I have the Matrix controller in a side panel and use Mushroom cards from HACs (https://github.com/piitaya/lovelace-mushroom).
All the relevant yamle is below: 

![image](https://github.com/PhillyGilly/Lindy4x4Matrix/assets/56273663/f0ccefd6-6e44-4631-8788-e903bcd8140c)

For the Sidebar Card
```
views:
  - type: masonry
    path: dashboard-mushroom
    cards:
      - type: horizontal-stack
        cards:
          - type: vertical-stack
            cards:
              - type: custom:mushroom-select-card
                entity: input_select.lindy_lounge
                name: ' '
                layout: vertical
                fill_container: false
                primary_info: none
                secondary_info: none
              - type: horizontal-stack
                cards:
                  - type: custom:mushroom-media-player-card
                    entity: media_player.lg_webos_tv_oled55c34la
                    layout: vertical
                    media_controls: []
                    collapsible_controls: false
                    name: LG TV
                    tap_action:
                      action: toggle
            title: Lounge
          - type: vertical-stack
            cards:
              - type: custom:mushroom-select-card
                entity: input_select.lindy_kitchen
                name: ' '
                layout: vertical
                fill_container: false
                primary_info: none
                secondary_info: none
              - type: horizontal-stack
                cards:
                  - type: custom:mushroom-media-player-card
                    entity: media_player.lg_webos_tv_55um7000plc
                    layout: vertical
                    media_controls:
                      - on_off
                    collapsible_controls: false
                    name: LG TV
            title: Kitchen
          - type: vertical-stack
            cards:
              - type: custom:mushroom-select-card
                entity: input_select.lindy_bed3
                name: ' '
                layout: vertical
                fill_container: false
                primary_info: none
                secondary_info: none
              - type: horizontal-stack
                cards:
                  - type: custom:mushroom-media-player-card
                    entity: media_player.lg_smart_tv
                    layout: vertical
                    media_controls: []
                    collapsible_controls: false
                    name: LG TV
                    tap_action:
                      action: toggle
            title: Bed 3
          - type: vertical-stack
            cards:
              - type: custom:mushroom-select-card
                entity: input_select.lindy_bed1
                name: ' '
                layout: vertical
                fill_container: false
                primary_info: none
                secondary_info: none
            title: Bed  1
```
In configuration.yaml add the line
```
shell_command:!include shell_command.yaml
```
This is the content of the shell_command.yaml
```
lounge_to_sky:        echo -e "\xa5\x5b\x02\x03\x01\x00\x01\x00\x00\x00\x00\x00\xf9" | nc 192.168.2.50 8000
lounge_to_firestick:  echo -e "\xa5\x5b\x02\x03\x02\x00\x01\x00\x00\x00\x00\x00\xf8" | nc 192.168.2.50 8000
lounge_to_dvd:        echo -e "\xa5\x5b\x02\x03\x03\x00\x01\x00\x00\x00\x00\x00\xf7" | nc 192.168.2.50 8000
lounge_to_cctv:       echo -e "\xa5\x5b\x02\x03\x04\x00\x01\x00\x00\x00\x00\x00\xf6" | nc 192.168.2.50 8000

kitchen_to_sky:       echo -e "\xa5\x5b\x02\x03\x01\x00\x02\x00\x00\x00\x00\x00\xf8" | nc 192.168.2.50 8000
kitchen_to_firestick: echo -e "\xa5\x5b\x02\x03\x02\x00\x02\x00\x00\x00\x00\x00\xf7" | nc 192.168.2.50 8000
kitchen_to_dvd:       echo -e "\xa5\x5b\x02\x03\x03\x00\x02\x00\x00\x00\x00\x00\xf6" | nc 192.168.2.50 8000
kitchen_to_cctv:      echo -e "\xa5\x5b\x02\x03\x04\x00\x02\x00\x00\x00\x00\x00\xf5" | nc 192.168.2.50 8000

spare_to_sky:         echo -e "\xa5\x5b\x02\x03\x01\x00\x03\x00\x00\x00\x00\x00\xf7" | nc 192.168.2.50 8000
spare_to_firestick:   echo -e "\xa5\x5b\x02\x03\x02\x00\x03\x00\x00\x00\x00\x00\xf6" | nc 192.168.2.50 8000
spare_to_dvd:         echo -e "\xa5\x5b\x02\x03\x03\x00\x03\x00\x00\x00\x00\x00\xf5" | nc 192.168.2.50 8000
spare_to_cctv:        echo -e "\xa5\x5b\x02\x03\x04\x00\x03\x00\x00\x00\x00\x00\xf4" | nc 192.168.2.50 8000

bed1_to_sky:          echo -e "\xa5\x5b\x02\x03\x01\x00\x04\x00\x00\x00\x00\x00\xf6" | nc 192.168.2.50 8000
bed1_to_firestick:    echo -e "\xa5\x5b\x02\x03\x02\x00\x04\x00\x00\x00\x00\x00\xf5" | nc 192.168.2.50 8000
bed1_to_dvd:          echo -e "\xa5\x5b\x02\x03\x03\x00\x04\x00\x00\x00\x00\x00\xf4" | nc 192.168.2.50 8000
bed1_to_cctv:         echo -e "\xa5\x5b\x02\x03\x04\x00\x04\x00\x00\x00\x00\x00\xf3" | nc 192.168.2.50 8000
```
Finally you need an automation to monitor changes in the input_selector and fire the shell_commands:
```
alias: 33c. Lindy new
description: ""
triggers:
  - entity_id:
      - input_select.lindy_lounge
    id: lounge
    trigger: state
  - entity_id:
      - input_select.lindy_kitchen
    id: kitchen
    trigger: state
  - entity_id:
      - input_select.lindy_bed1
    id: bed1
    trigger: state
  - entity_id:
      - input_select.lindy_bed3
    id: bed3
    trigger: state
  - at: "03:00:00"
    id: three
    trigger: time
conditions: []
actions:
  - choose:
      - conditions:
          - condition: trigger
            id:
              - lounge
        sequence:
          - if:
              - condition: state
                entity_id: input_select.lindy_lounge
                state: Sky
            then:
              - data: {}
                action: shell_command.lounge_to_sky
          - if:
              - condition: state
                entity_id: input_select.lindy_lounge
                state: Firestick
            then:
              - data: {}
                action: shell_command.lounge_to_firestick
          - if:
              - condition: state
                entity_id: input_select.lindy_lounge
                state: DVD
            then:
              - data: {}
                action: shell_command.lounge_to_dvd
          - if:
              - condition: state
                entity_id: input_select.lindy_lounge
                state: CCTV
            then:
              - data: {}
                action: shell_command.lounge_to_cctv
      - conditions:
          - condition: trigger
            id:
              - kitchen
        sequence:
          - if:
              - condition: state
                entity_id: input_select.lindy_kitchen
                state: Sky
            then:
              - data: {}
                action: shell_command.kitchen_to_sky
          - if:
              - condition: state
                entity_id: input_select.lindy_kitchen
                state: Firestick
            then:
              - data: {}
                action: shell_command.kitchen_to_firestick
          - if:
              - condition: state
                entity_id: input_select.lindy_kitchen
                state: CCTV
            then:
              - data: {}
                action: shell_command.kitchen_to_cctv
          - if:
              - condition: state
                entity_id: input_select.lindy_kitchen
                state: DVD
            then:
              - data: {}
                action: shell_command.kitchen_to_dvd
      - conditions:
          - condition: trigger
            id:
              - bed1
        sequence:
          - if:
              - condition: state
                entity_id: input_select.lindy_bed1
                state: Sky
            then:
              - data: {}
                action: shell_command.bed1_to_sky
          - if:
              - condition: state
                entity_id: input_select.lindy_bed1
                state: Firestick
            then:
              - data: {}
                action: shell_command.bed1_to_firestick
          - if:
              - condition: state
                entity_id: input_select.lindy_bed1
                state: DVD
            then:
              - data: {}
                action: shell_command.bed1_to_dvd
          - if:
              - condition: state
                entity_id: input_select.lindy_bed1
                state: CCTV
            then:
              - data: {}
                action: shell_command.bed1_to_cctv
      - conditions:
          - condition: trigger
            id:
              - bed3
        sequence:
          - if:
              - condition: state
                entity_id: input_select.lindy_bed3
                state: Sky
            then:
              - data: {}
                action: shell_command.bed3_to_sky
          - if:
              - condition: state
                entity_id: input_select.lindy_bed3
                state: Firestick
            then:
              - data: {}
                action: shell_command.bed3_to_firestick
          - if:
              - condition: state
                entity_id: input_select.lindy_bed3
                state: DVD
            then:
              - data: {}
                action: shell_command.bed3_to_dvd
          - if:
              - condition: state
                entity_id: input_select.lindy_bed3
                state: CCTV
            then:
              - data: {}
                action: shell_command.bed3_to_cctv
      - conditions:
          - condition: trigger
            id:
              - three
        sequence:
          - if:
              - condition: not
                conditions:
                  - condition: state
                    entity_id: input_select.lindy_lounge
                    state: Sky
            then:
              - data: {}
                action: shell_command.lounge_to_sky
              - metadata: {}
                data:
                  option: Sky
                target:
                  entity_id: input_select.lindy_lounge
                action: input_select.select_option
          - if:
              - condition: not
                conditions:
                  - condition: state
                    entity_id: input_select.lindy_kitchen
                    state: Sky
            then:
              - data: {}
                action: shell_command.kitchen_to_sky
              - metadata: {}
                data:
                  option: Sky
                target:
                  entity_id: input_select.lindy_kitchen
                action: input_select.select_option
          - if:
              - condition: not
                conditions:
                  - condition: state
                    entity_id: input_select.lindy_bed1
                    state: Sky
            then:
              - data: {}
                action: shell_command.bed1_to_sky
              - metadata: {}
                data:
                  option: Sky
                target:
                  entity_id: input_select.lindy_bed1
                action: input_select.select_option
          - if:
              - condition: not
                conditions:
                  - condition: state
                    entity_id: input_select.lindy_bed3
                    state: Sky
            then:
              - data: {}
                action: shell_command.bed3_to_sky
              - metadata: {}
                data:
                  option: Sky
                target:
                  entity_id: input_select.lindy_bed3
                action: input_select.select_option
          - delay:
              hours: 0
              minutes: 0
              seconds: 10
              milliseconds: 0
          - target:
              entity_id:
                - switch.lindy_hdmi_matrix
            data: {}
            action: switch.turn_off
mode: single
```
