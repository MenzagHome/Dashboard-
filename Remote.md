type: vertical-stack
cards:
  - type: horizontal-stack
    cards:
      - show_name: true
        show_icon: false
        type: button
        tap_action:
          action: none
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: false
        type: button
        tap_action:
          action: none
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: false
        show_icon: true
        type: button
        icon: mdi:power
        tap_action:
          action: call-service
          service: remote.send_command
          target:
            entity_id: remote.pop
          data:
            command: POWER
        hold_action:
          action: none
        theme: 3ative_blue
        show_state: true
  - square: false
    type: grid
    cards:
      - type: picture
        tap_action:
          action: call-service
          service: remote.turn_on
          target:
            entity_id: remote.pop
          data:
            activity: https://www.netflix.com/title
        hold_action:
          action: none
        image: https://upload.wikimedia.org/wikipedia/commons/7/7a/Logonetflix.png
        theme: 3ative_blue
      - type: picture
        tap_action:
          action: call-service
          service: remote.turn_on
          target:
            entity_id: remote.pop
          data:
            activity: com.amazon.music.tv
        hold_action:
          action: none
        theme: 3ative_blue
        image: >-
          https://upload.wikimedia.org/wikipedia/commons/5/5f/Amazonmusic.logo.png
      - type: picture
        tap_action:
          action: call-service
          service: remote.turn_on
          data:
            activity: https://app.primevideo.com
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        image: >-
          https://upload.wikimedia.org/wikipedia/commons/thumb/1/11/Amazon_Prime_Video_logo.svg/1280px-Amazon_Prime_Video_logo.svg.png
        theme: 3ative_blue
      - type: picture
        tap_action:
          action: call-service
          service: remote.turn_on
          data:
            activity: https://www.youtube.com/
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
        image: >-
          https://upload.wikimedia.org/wikipedia/commons/thumb/b/b8/YouTube_Logo_2017.svg/1024px-YouTube_Logo_2017.svg.png
      - show_name: true
        show_icon: false
        type: button
        tap_action:
          action: none
        hold_action:
          action: none
        theme: 3ative_blue
      - type: picture
        tap_action:
          action: call-service
          service: remote.turn_on
          data:
            activity: https://www.disneyplus.com
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        image: https://lumiere-a.akamaihd.net/v1/images/en_dplus_lg_r_2x_54572343.png
        theme: 3ative_blue
    columns: 3
  - square: false
    type: grid
    cards:
      - show_name: true
        show_icon: false
        type: button
        tap_action:
          action: none
        hold_action:
          action: none
        theme: 3ative_blue
      - type: picture
        tap_action:
          action: call-service
          service: androidtv.adb_command
          target:
            entity_id: media_player.android_tv_192_168_1_9
          data:
            command: am start net.oqee.androidtv/.MainActivity
        hold_action:
          action: none
        theme: 3ative_blue
        image: https://upload.wikimedia.org/wikipedia/commons/5/52/Logo_de_Free.png
      - show_name: true
        show_icon: false
        type: button
        tap_action:
          action: none
        hold_action:
          action: none
        theme: 3ative_blue
    columns: 3
  - square: false
    columns: 3
    type: grid
    cards:
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:keyboard-return
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: BACK
          target:
            entity_id: remote.pop
        hold_action:
          action: call-service
          service: remote.send_command
          data:
            command: BACK
            hold_secs: 0.5
          target:
            entity_id: remote.pop
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:arrow-up-bold
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: DPAD_UP
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:home-outline
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: HOME
          target:
            entity_id: remote.pop
        hold_action:
          action: call-service
          service: remote.send_command
          data:
            command: HOME
            hold_secs: 0.5
          target:
            entity_id: remote.pop
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:arrow-left-bold
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: DPAD_LEFT
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:circle
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: DPAD_CENTER
          target:
            entity_id: remote.pop
        hold_action:
          action: call-service
          service: remote.send_command
          data:
            command: DPAD_CENTER
            hold_secs: 0.5
          target:
            entity_id: remote.pop
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:arrow-right-bold
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: DPAD_RIGHT
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: false
        type: button
        tap_action:
          action: none
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:arrow-down-bold
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: DPAD_DOWN
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: false
        type: button
        tap_action:
          action: none
        hold_action:
          action: none
        theme: 3ative_blue
  - square: false
    columns: 3
    type: grid
    cards:
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:volume-high
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: VOLUME_UP
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:volume-off
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: MUTE
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:plus-thick
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: CHANNEL_UP
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:volume-medium
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: VOLUME_DOWN
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        tap_action:
          action: call-service
          service: remote.send_command
          target:
            entity_id: remote.pop
          data:
            command: 0
        hold_action:
          action: none
        theme: 3ative_blue
        icon: mdi:numeric-0
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:minus-thick
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: CHANNEL_DOWN
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
  - square: false
    columns: 3
    type: grid
    cards:
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:numeric-1
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: 1
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:numeric-2
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: 2
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:numeric-3
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: 3
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:numeric-4
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: 4
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:numeric-5
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: 5
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:numeric-6
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: 6
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        tap_action:
          action: call-service
          service: remote.send_command
          target:
            entity_id: remote.pop
          data:
            command: 7
        hold_action:
          action: none
        theme: 3ative_blue
        icon: mdi:numeric-7
      - show_name: true
        show_icon: true
        type: button
        icon: mdi:numeric-8
        tap_action:
          action: call-service
          service: remote.send_command
          data:
            command: 8
          target:
            entity_id: remote.pop
        hold_action:
          action: none
        theme: 3ative_blue
      - show_name: true
        show_icon: true
        type: button
        tap_action:
          action: call-service
          service: remote.send_command
          target: {}
          data:
            command: 9
        hold_action:
          action: none
        theme: 3ative_blue
        icon: mdi:numeric-9
  - type: entity
    entity: remote.pop
    attribute: current_activity
    theme: 3ative_blue
    state_color: true
  - type: media-control
    entity: media_player.android_tv_192_168_1_9
    theme: 3ative_blue
