button_card_templates:
  bouton_hex_style:
    show_name: false
    show_state: false
    styles:
      icon:
        - width: 18px
        - height: 18px
        - color: |
            [[[
              const parentOn = states['light.salon_2'].state === 'on';
              const active = entity.state === 'on' || entity.state === 'open';
              if (active) return parentOn ? '#ffc107' : '#00e5ff';
              return parentOn ? 'rgba(255,193,7,0.55)' : 'rgba(0,229,255,0.55)';
            ]]]
        - filter: |
            [[[
              const parentOn = states['light.salon_2'].state === 'on';
              const active = entity.state === 'on' || entity.state === 'open';
              if (active) {
                const c = parentOn ? '#ffc107' : '#00e5ff';
                return `drop-shadow(0 0 6px ${c})`;
              }
              return 'none';
            ]]]
      card:
        - background: |
            [[[
              const parentOn = states['light.salon_2'].state === 'on';
              const active = entity.state === 'on' || entity.state === 'open';
              if (active) return parentOn ? 'rgba(255,193,7,0.15)' : 'rgba(0,229,255,0.15)';
              return parentOn ? 'rgba(255,193,7,0.06)' : 'rgba(0,229,255,0.06)';
            ]]]
        - clip-path: >-
            polygon(8px 0%, calc(100% - 8px) 0%, 100% 50%, calc(100% - 8px)
            100%, 8px 100%, 0% 50%)
        - height: 32px
    tap_action:
      action: toggle
views:
  - title: ' '
    sections:
      - type: grid
        cards:
          - square: false
            type: grid
            cards:
              - type: custom:button-card
                entity: input_boolean.poubelles
                show_name: false
                show_state: false
                show_icon: false
                styles:
                  card:
                    - height: 170px
                    - border-radius: 6px
                    - background: |
                        [[[
                          return entity.state === 'on' ? 'rgba(255,23,68,0.03)' : 'rgba(0,229,255,0.03)';
                        ]]]
                    - border: |
                        [[[
                          return entity.state === 'on' ? '1px solid rgba(255,23,68,0.45)' : '1px solid rgba(0,229,255,0.2)';
                        ]]]
                    - box-shadow: |
                        [[[
                          return entity.state === 'on' ? '0 0 40px rgba(255,23,68,0.15)' : '0 0 40px rgba(0,229,255,0.06)';
                        ]]]
                    - padding: 0
                    - overflow: visible
                    - position: relative
                  grid:
                    - grid-template-areas: '"visuel jour" "evenement evenement"'
                    - grid-template-columns: 130px 1fr
                    - grid-template-rows: 102px 68px
                  custom_fields:
                    visuel:
                      - justify-self: center
                      - align-self: center
                    jour:
                      - justify-self: center
                      - align-self: center
                    evenement:
                      - justify-self: stretch
                      - align-self: stretch
                custom_fields:
                  visuel: |
                    [[[
                      const isAlert = entity.state === 'on';

                      if (isAlert) {
                        const poubelle = states['sensor.couleur_poubelle']?.state || '';
                        let icon = 'mdi:trash-can';
                        let color = '#ff1744';

                        if (poubelle === 'Sortir la Poubelle Jaune') {
                          icon = 'mdi:trash-can';
                          color = '#ffc107';
                        } else if (poubelle === 'Sortir la Poubelle Grise') {
                          icon = 'mdi:trash-can-outline';
                          color = '#9e9e9e';
                        }

                        return `<div style="
                          display:flex; align-items:center; justify-content:center;
                          width:100%; height:102px;
                        ">
                          <ha-icon icon="${icon}" style="
                            width:70px; height:70px; color:${color};
                            filter: drop-shadow(0 0 12px ${color}) drop-shadow(0 0 24px ${color}66);
                            animation: blink 2 ease infinite;
                          "></ha-icon>
                        </div>`;
                      }

                      // Calendrier (OFF)
                      const d_obj = new Date();
                      const t_day = d_obj.getDate();
                      const t_month = d_obj.getMonth();
                      const t_year = d_obj.getFullYear();
                      const f_day = new Date(t_year, t_month, 1).getDay();
                      const days_in_m = new Date(t_year, t_month + 1, 0).getDate();
                      let start_idx = f_day === 0 ? 6 : f_day - 1;

                      let cal_content = `
                        <style>
                          .cal-grid {
                            display: grid;
                            grid-template-columns: repeat(7, 1fr);
                            gap: 2px;
                            width: 110px;
                            font-family: 'Inter', sans-serif;
                            font-size: 9px;
                            text-align: center;
                          }
                          .cal-day-num { color: rgba(0,229,255,0.45); padding: 1px 0; }
                          .is-today {
                            background-color: rgba(0,229,255,0.15);
                            color: #00e5ff !important;
                            border: 1px solid rgba(0,229,255,0.6);
                            border-radius: 4px;
                            font-weight: bold;
                            box-shadow: 0 0 8px rgba(0,229,255,0.5);
                          }
                          .cal-header-month {
                            color: #00e5ff;
                            font-family: Orbitron, sans-serif;
                            font-weight: bold;
                            grid-column: span 7;
                            font-size: 10px;
                            letter-spacing: 2px;
                            text-transform: uppercase;
                            text-shadow: 0 0 8px rgba(0,229,255,0.6);
                            margin-bottom: 4px;
                          }
                          .day-name {
                            font-size: 7px;
                            color: rgba(0,229,255,0.6);
                            font-weight: bold;
                            padding-bottom: 2px;
                          }
                        </style>
                        <div class="cal-grid">
                          <div class="cal-header-month">${new Intl.DateTimeFormat('fr-FR', { month: 'long' }).format(d_obj)}</div>
                          <div class="day-name">L</div><div class="day-name">M</div><div class="day-name">M</div>
                          <div class="day-name">J</div><div class="day-name">V</div><div class="day-name">S</div><div class="day-name">D</div>
                      `;
                      for (let i = 0; i < start_idx; i++) { cal_content += `<div></div>`; }
                      for (let d = 1; d <= days_in_m; d++) {
                        const cls = d === t_day ? 'cal-day-num is-today' : 'cal-day-num';
                        cal_content += `<div class="${cls}">${d}</div>`;
                      }
                      cal_content += `</div>`;
                      return cal_content;
                    ]]]
                  jour: |
                    [[[
                      const isAlert = entity.state === 'on';
                      const jour_val = states['sensor.jour'] ? states['sensor.jour'].state : '';
                      const mois_val = states['sensor.nom_mois'] ? states['sensor.nom_mois'].state : '';
                      const color = isAlert ? '#ff1744' : '#00e5ff';
                      const label = isAlert
                        ? (states['sensor.couleur_poubelle']?.state || 'Sortir la poubelle')
                        : `${jour_val} ${mois_val}`;
                      const fontSize = isAlert ? '17px' : '32px';

                      return `<div style="
                        display:flex; align-items:center; justify-content:start;
                        text-align:center; height:100%; width:100%;
                        font-family: Orbitron, sans-serif;
                        font-size: ${fontSize};
                        font-weight: 700;
                        letter-spacing: 2px;
                        color: ${color};
                        text-shadow: 0 0 20px ${color}, 0 0 40px ${color}40;
                        white-space: nowrap;
                        padding: 0 10px;
                      ">${label}</div>`;
                    ]]]
                  evenement: |
                    [[[
                      const isAlert = entity.state === 'on';
                      const color = isAlert ? '#ff1744' : '#00e5ff';
                      const event_name = states['sensor.prochain_evenement'] ? states['sensor.prochain_evenement'].state : 'Aucun événement';
                      const debut_val = states['sensor.debut_prochain_evenement'] ? states['sensor.debut_prochain_evenement'].state : '--:--';

                      return `
                        <style>
                          .evt-wrapper {
                            display:flex; align-items:center; gap:8px;
                            height:68px; padding:0 12px;
                            border-top:1px solid ${color}22;
                          }
                          .evt-time {
                            font-family: Orbitron, sans-serif;
                            font-size: 13px;
                            font-weight: 700;
                            color: ${color};
                            text-shadow: 0 0 8px ${color}aa;
                            white-space: nowrap;
                            flex-shrink: 0;
                          }
                          .evt-scroll {
                            flex: 1;
                            overflow: hidden;
                            white-space: nowrap;
                            position: relative;
                          }
                          .evt-text {
                            display: inline-block;
                            font-family: 'Inter', sans-serif;
                            font-size: 13px;
                            color: rgba(255,255,255);
                            animation: evt-scroll-anim 12 linear infinite;
                          }
                          @keyframes evt-scroll-anim {
                            0%   { transform: translateX(100%); }
                            100% { transform: translateX(-100%); }
                          }
                        </style>
                        <div class="evt-wrapper">
                          <span class="evt-time">${debut_val}</span>
                          <div class="evt-scroll">
                            <span class="evt-text">${event_name}</span>
                          </div>
                        </div>
                      `;
                    ]]]
                  DecoLines: |
                    [[[
                      const isAlert = entity.state === 'on';
                      const c = isAlert ? '#ff1744' : '#00e5ff';
                      const cOff = isAlert ? 'rgba(255,23,68,0.18)' : 'rgba(0,229,255,0.18)';
                      const divLine = isAlert ? 'rgba(255,23,68,0.15)' : 'rgba(0,229,255,0.12)';
                      const topLine = isAlert
                        ? 'linear-gradient(90deg,transparent,#ff1744 30%,#ff1744 70%,transparent)'
                        : 'linear-gradient(90deg,transparent,#00e5ff 30%,#00e5ff 70%,transparent)';
                      return `
                        <style>
                          @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }
                        </style>
                        <div style="position:absolute;top:0;left:0;right:0;height:1px;background:${topLine};opacity:0.65;pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;bottom:68px;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,${divLine},transparent);pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;top:-1px;left:-1px;width:12px;height:12px;border-top:2px solid ${c};border-left:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;top:-1px;right:-1px;width:12px;height:12px;border-top:2px solid ${c};border-right:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;left:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-left:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;right:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-right:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                      `;
                    ]]]
                tap_action:
                  action: navigate
                  navigation_path: /lovelace/vie-courante
                double_tap_action:
                  action: call-service
                  service: input_boolean.turn_off
                  service_data:
                    entity_id: input_boolean.poubelles
                hold_action:
                  action: call-service
                  service: input_boolean.turn_off
                  service_data:
                    entity_id: input_boolean.poubelles
              - type: custom:button-card
                entity: light.salon_2
                show_name: false
                show_state: false
                show_icon: false
                styles:
                  card:
                    - height: 170px
                    - border-radius: 6px
                    - background: |
                        [[[
                          return states['light.salon_2'].state === 'on' ? 'rgba(255,193,7,0.03)' : 'rgba(0,229,255,0.03)';
                        ]]]
                    - border: |
                        [[[
                          return states['light.salon_2'].state === 'on' ? '1px solid rgba(255,193,7,0.22)' : '1px solid rgba(0,229,255,0.2)';
                        ]]]
                    - box-shadow: |
                        [[[
                          return states['light.salon_2'].state === 'on' ? '0 0 40px rgba(255,193,7,0.07)' : '0 0 40px rgba(0,229,255,0.06)';
                        ]]]
                    - padding: 0
                    - overflow: visible
                    - position: relative
                  grid:
                    - grid-template-areas: '"ico_main titre_salon" "b_bas b_bas"'
                    - grid-template-columns: 95px 1fr
                    - grid-template-rows: 102px 68px
                custom_fields:
                  titre_salon: |
                    [[[
                      const color = states['light.salon_2'].state === 'on' ? '#ffc107' : '#00e5ff';
                      return `<div style="
                        display: flex;
                        align-items: center;
                        justify-content: center;
                        text-align: center;
                        height: 100%;
                        width: 100%;
                        font-family: Orbitron, sans-serif;
                        font-size: 32px;
                        font-weight: 700;
                        letter-spacing: 4px;
                        color: ${color};
                        text-shadow: 0 0 20px ${color}, 0 0 40px ${color}40;
                      ">Séjour</div>`;
                    ]]]
                  ico_main:
                    card:
                      type: custom:button-card
                      entity: light.salon_2
                      show_name: false
                      show_state: false
                      icon: |
                        [[[
                          if (states['light.salon_2'].state !== 'on' && states['climate.poele_salon']?.state === 'heat') return 'mdi:fireplace';
                          return states['light.salon_2'].state === 'on' ? 'phu:living-room' : 'mdi:sofa';
                        ]]]
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - height: 102px
                          - width: 95px
                          - border-right: |
                              [[[
                                return states['light.salon_2'].state === 'on' ? '1px solid rgba(255,193,7,0.15)' : '1px solid rgba(0,229,255,0.12)';
                              ]]]
                          - border-radius: 0
                        icon:
                          - width: 75px
                          - height: 75px
                          - color: |
                              [[[
                                if (states['light.salon_2'].state !== 'on' && states['climate.poele_salon']?.state === 'heat') return '#FFA500';
                                return states['light.salon_2'].state === 'on' ? '#ffc107' : '#00e5ff';
                              ]]]
                          - filter: |
                              [[[
                                const c = states['light.salon_2'].state === 'on' ? 'rgba(255,193,7' : 'rgba(0,229,255';
                                return `drop-shadow(0 0 12px ${c},0.9)) drop-shadow(0 0 25px ${c},0.4))`;
                              ]]]
                          - animation: |
                              [[[
                                return (states['light.salon_2'].state !== 'on' && states['climate.poele_salon']?.state === 'heat') ? 'flamme 0.9s ease-in-out infinite' : 'none';
                              ]]]
                  b_bas:
                    card:
                      type: custom:button-card
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - padding: 0 6px
                          - height: 68px
                          - display: flex
                          - align-items: flex-end
                          - overflow: visible
                        grid:
                          - grid-template-columns: repeat(6, 1fr)
                          - grid-template-areas: '"b1 b2 b3 b4 b5 b6"'
                          - gap: 4px
                          - align-items: flex-end
                      custom_fields:
                        b1:
                          card:
                            type: custom:button-card
                            entity: light.prise_tv_switch
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return states['light.prise_tv_switch'].state
                              === 'on' ? 'mdi:television-speaker' :
                              'mdi:television-speaker-off'; ]]]
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                              - value: unavailable
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: call-service
                              service: light.toggle
                              service_data:
                                entity_id: light.prise_tv_switch
                        b2:
                          card:
                            type: custom:button-card
                            entity: light.salon
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return states['light.salon'].state === 'on' ?
                              'mdi:sofa' : 'mdi:sofa-outline'; ]]]
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                              - value: unavailable
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: call-service
                              service: input_boolean.toggle
                              service_data:
                                entity_id: input_boolean.canape_salon
                            hold_action:
                              action: more-info
                              entity: light.salon
                        b3:
                          card:
                            type: custom:button-card
                            entity: light.lampes_salle_a_manger
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return
                              states['light.lampes_salle_a_manger'].state ===
                              'on' ? 'mdi:lamps' : 'mdi:lamps-outline'; ]]]
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                              - value: unavailable
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: call-service
                              service: input_boolean.toggle
                              service_data:
                                entity_id: input_boolean.lampes
                            hold_action:
                              action: more-info
                              entity: light.lampes_salle_a_manger
                        b4:
                          card:
                            type: custom:button-card
                            entity: light.luminaire_salle_a_manger_lumiere
                            show_name: false
                            show_state: false
                            icon: mdi:light-recessed
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                              - value: unavailable
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: call-service
                              service: light.toggle
                              service_data:
                                entity_id: light.luminaire_salle_a_manger_lumiere
                            hold_action:
                              action: more-info
                              entity: light.luminaire_salle_a_manger_lumiere
                        b5:
                          card:
                            type: custom:button-card
                            entity: light.ambilight
                            show_name: false
                            show_state: false
                            icon: mdi:television-ambient-light
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                              - value: unavailable
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: call-service
                              service: light.toggle
                              service_data:
                                entity_id: light.ambilight
                            hold_action:
                              action: fire-dom-event
                              browser_mod:
                                service: browser_mod.popup
                                data:
                                  title: AMBILIGHT
                                  style: >
                                    --popup-background: rgba(0, 5, 15, 0.96);
                                    --popup-border-radius: 6px;
                                    --popup-border-color: rgba(0,229,255,0.22);
                                    --popup-box-shadow: 0 0 40px
                                    rgba(0,229,255,0.08); --ha-card-background:
                                    transparent; --mdc-theme-surface: rgba(0, 5,
                                    15, 0.96); --primary-text-color: #00e5ff;
                                    --secondary-text-color:
                                    rgba(0,229,255,0.55); --mdc-theme-primary:
                                    #00e5ff; --paper-item-icon-color: #00e5ff;
                                    --slider-color: #00e5ff;
                                    --slider-secondary-color:
                                    rgba(0,229,255,0.2); backdrop-filter:
                                    blur(12px);
                                  content:
                                    type: entities
                                    entities:
                                      - entity: light.ambilight
                                        name: Ambilight
                                      - entity: select.selection_hdmi_ambilight
                                        name: Source HDMI
                                      - entity: select.scenes_ambilight
                                        name: Scène
                                      - entity: select.reglage_eclairage_ambilight
                                        name: Éclairage
                                      - entity: number.rapidite_de_diffusion_ambilight
                                        name: Rapidité
                        b6:
                          card:
                            type: custom:button-card
                            entity: cover.volet_salon
                            show_name: false
                            show_state: false
                            icon: |
                              [[[
                                if (states["binary_sensor.ouvertures_sejour"]?.state === "on") return "mdi:window-open-variant";
                                if (states["switch.chauffage_au_sol"]?.state === "on") return "mdi:heating-coil";
                                return states["cover.volet_salon"]?.state === "open" ? "mdi:window-shutter-open" : "mdi:window-shutter";
                              ]]]
                            styles:
                              icon:
                                - color: |
                                    [[[
                                      if (states['binary_sensor.ouvertures_sejour']?.state === 'on' || states['switch.chauffage_au_sol']?.state === 'on') return 'rgba(255,23,68,0.9)';
                                      return states['cover.volet_salon']?.state === 'open' ? '#ffc107' : 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      if (states['binary_sensor.ouvertures_sejour']?.state === 'on' || states['switch.chauffage_au_sol']?.state === 'on') return 'drop-shadow(0 0 5px rgba(255,23,68,0.6))';
                                      return states['cover.volet_salon']?.state === 'open' ? 'drop-shadow(0 0 6px rgba(255,193,7,0.8))' : 'none';
                                    ]]]
                                - animation: |
                                    [[[
                                      return (states['binary_sensor.ouvertures_sejour']?.state === 'on' || states['switch.chauffage_au_sol']?.state === 'on') ? 'blink 2 ease infinite' : 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      if (states['binary_sensor.ouvertures_sejour']?.state === 'on' || states['switch.chauffage_au_sol']?.state === 'on') return 'rgba(255,23,68,0.1)';
                                      return states['cover.volet_salon']?.state === 'open' ? 'rgba(255,193,7,0.12)' : 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      if (states['binary_sensor.ouvertures_sejour']?.state === 'on' || states['switch.chauffage_au_sol']?.state === 'on') return '1px solid rgba(255,23,68,0.45)';
                                      return states['cover.volet_salon']?.state === 'open' ? '1px solid rgba(255,193,7,0.45)' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - border-radius: 0 0 8px 0
                                - box-shadow: |
                                    [[[
                                      if (states['binary_sensor.ouvertures_sejour']?.state === 'on' || states['switch.chauffage_au_sol']?.state === 'on') return '2px 4px 14px rgba(255,23,68,0.18), inset 0 -3px 0 #ff1744';
                                      return states['cover.volet_salon']?.state === 'open' ? '2px 4px 14px rgba(255,193,7,0.18), inset 0 -3px 0 #ffc107' : 'none';
                                    ]]]
                                - height: |
                                    [[[
                                      return (states['cover.volet_salon']?.state === 'open' || states['binary_sensor.ouvertures_sejour']?.state === 'on' || states['switch.chauffage_au_sol']?.state === 'on') ? '52px' : '42px';
                                    ]]]
                            tap_action:
                              action: call-service
                              service: cover.toggle
                              service_data:
                                entity_id: cover.volet_salon
                            hold_action:
                              action: more-info
                              entity: cover.volet_salon
                  DecoLines: |
                    [[[
                      const isOn = states['light.salon_2'].state === 'on';
                      const c = isOn ? '#ffc107' : '#00e5ff';
                      const cOff = isOn ? 'rgba(255,193,7,0.18)' : 'rgba(0,229,255,0.18)';
                      const divLine = isOn ? 'rgba(255,193,7,0.15)' : 'rgba(0,229,255,0.12)';
                      const topLine = isOn ? 'linear-gradient(90deg,transparent,#ffc107 30%,#ffc107 70%,transparent)' : 'linear-gradient(90deg,transparent,#00e5ff 30%,#00e5ff 70%,transparent)';
                      return `
                        <style>
                          @keyframes flamme {
                            0%   { filter:drop-shadow(0 0 5px #ff4500); transform:scale(1); }
                            50%  { filter:drop-shadow(0 0 15px #ff8c00); transform:scale(1.05) translateY(-1px); }
                            100% { filter:drop-shadow(0 0 5px #ff4500); transform:scale(1); }
                          }
                          @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }
                        </style>
                        <div style="position:absolute;top:0;left:0;right:0;height:1px;background:${topLine};opacity:0.65;pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;bottom:68px;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,${divLine},transparent);pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;top:-1px;left:-1px;width:12px;height:12px;border-top:2px solid ${c};border-left:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;top:-1px;right:-1px;width:12px;height:12px;border-top:2px solid ${c};border-right:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;left:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-left:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;right:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-right:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                      `;
                    ]]]
                hold_action:
                  action: navigate
                  navigation_path: /lovelace/sejour
                tap_action:
                  action: fire-dom-event
                  honeycomb_menu:
                    autoclose: true
                    size: 210
                    filter_radius: 100
                    style:
                      - background: rgba(0,5,15,0.92)
                      - border: 1px solid rgba(0,229,255,0.25)
                      - box-shadow: 0 0 40px rgba(0,229,255,0.1)
                      - border-radius: 6px
                    buttons:
                      - entity: input_boolean.effets_lumineux
                        icon: mdi:star-four-points
                        state:
                          - value: 'on'
                            styles:
                              icon:
                                - color: '#ffc107'
                                - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                              card:
                                - background: rgba(255,193,7,0.12)
                                - border: 1px solid rgba(255,193,7,0.4)
                                - box-shadow: 0 0 15px rgba(255,193,7,0.2)
                          - value: 'off'
                            styles:
                              icon:
                                - color: rgba(0,229,255,0.35)
                              card:
                                - background: rgba(0,10,22,0.85)
                                - border: 1px solid rgba(0,229,255,0.2)
                        tap_action:
                          action: call-service
                          service: input_boolean.toggle
                          service_data:
                            entity_id: input_boolean.effets_lumineux
                      - entity: input_boolean.noel
                        icon: mdi:pine-tree
                        state:
                          - value: 'on'
                            styles:
                              icon:
                                - color: '#ffc107'
                                - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                              card:
                                - background: rgba(255,193,7,0.12)
                                - border: 1px solid rgba(255,193,7,0.4)
                                - box-shadow: 0 0 15px rgba(255,193,7,0.2)
                          - value: 'off'
                            styles:
                              icon:
                                - color: rgba(0,229,255,0.35)
                              card:
                                - background: rgba(0,10,22,0.85)
                                - border: 1px solid rgba(0,229,255,0.2)
                        tap_action:
                          action: call-service
                          service: input_boolean.toggle
                          service_data:
                            entity_id: input_boolean.noel
                      - entity: input_boolean.halloween
                        icon: mdi:ghost
                        state:
                          - value: 'on'
                            styles:
                              icon:
                                - color: '#ffc107'
                                - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                              card:
                                - background: rgba(255,193,7,0.12)
                                - border: 1px solid rgba(255,193,7,0.4)
                                - box-shadow: 0 0 15px rgba(255,193,7,0.2)
                          - value: 'off'
                            styles:
                              icon:
                                - color: rgba(0,229,255,0.35)
                              card:
                                - background: rgba(0,10,22,0.85)
                                - border: 1px solid rgba(0,229,255,0.2)
                        tap_action:
                          action: call-service
                          service: input_boolean.toggle
                          service_data:
                            entity_id: input_boolean.halloween
                      - entity: light.prise_poele
                        icon: mdi:fire
                        state:
                          - value: 'on'
                            styles:
                              icon:
                                - color: '#ffc107'
                                - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                              card:
                                - background: rgba(255,193,7,0.12)
                                - border: 1px solid rgba(255,193,7,0.4)
                                - box-shadow: 0 0 15px rgba(255,193,7,0.2)
                          - value: 'off'
                            styles:
                              icon:
                                - color: rgba(0,229,255,0.35)
                              card:
                                - background: rgba(0,10,22,0.85)
                                - border: 1px solid rgba(0,229,255,0.2)
                        tap_action:
                          action: call-service
                          service: light.toggle
                          service_data:
                            entity_id: light.prise_poele
                      - entity: light.prise_arriere_tv
                        icon: mdi:power-plug
                        state:
                          - value: 'on'
                            styles:
                              icon:
                                - color: '#ffc107'
                                - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                              card:
                                - background: rgba(255,193,7,0.12)
                                - border: 1px solid rgba(255,193,7,0.4)
                                - box-shadow: 0 0 15px rgba(255,193,7,0.2)
                          - value: 'off'
                            styles:
                              icon:
                                - color: rgba(0,229,255,0.35)
                              card:
                                - background: rgba(0,10,22,0.85)
                                - border: 1px solid rgba(0,229,255,0.2)
                        tap_action:
                          action: call-service
                          service: light.toggle
                          service_data:
                            entity_id: light.prise_arriere_tv
                      - entity: input_boolean.lecture
                        icon: mdi:book-open-variant
                        state:
                          - value: 'on'
                            styles:
                              icon:
                                - color: '#ffc107'
                                - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                              card:
                                - background: rgba(255,193,7,0.12)
                                - border: 1px solid rgba(255,193,7,0.4)
                                - box-shadow: 0 0 15px rgba(255,193,7,0.2)
                          - value: 'off'
                            styles:
                              icon:
                                - color: rgba(0,229,255,0.35)
                              card:
                                - background: rgba(0,10,22,0.85)
                                - border: 1px solid rgba(0,229,255,0.2)
                        tap_action:
                          action: call-service
                          service: input_boolean.toggle
                          service_data:
                            entity_id: input_boolean.lecture
              - type: custom:button-card
                entity: light.cuisine_2
                show_name: false
                show_state: false
                show_icon: false
                styles:
                  card:
                    - height: 170px
                    - border-radius: 6px
                    - background: |
                        [[[
                          if (states['input_boolean.lave_vaisselle_vide'].state === 'on') return 'rgba(255,23,68,0.03)';
                          return states['light.cuisine_2'].state === 'on' ? 'rgba(255,193,7,0.03)' : 'rgba(0,229,255,0.03)';
                        ]]]
                    - border: |
                        [[[
                          if (states['input_boolean.lave_vaisselle_vide'].state === 'on') return '1px solid rgba(255,23,68,0.5)';
                          return states['light.cuisine_2'].state === 'on' ? '1px solid rgba(255,193,7,0.22)' : '1px solid rgba(0,229,255,0.2)';
                        ]]]
                    - box-shadow: |
                        [[[
                          if (states['input_boolean.lave_vaisselle_vide'].state === 'on') return '0 0 40px rgba(255,23,68,0.18)';
                          return states['light.cuisine_2'].state === 'on' ? '0 0 40px rgba(255,193,7,0.07)' : '0 0 40px rgba(0,229,255,0.06)';
                        ]]]
                    - padding: 0
                    - overflow: visible
                    - position: relative
                  grid:
                    - grid-template-areas: '"ico_main titre_cuisine" "b_bas b_bas"'
                    - grid-template-columns: 95px 1fr
                    - grid-template-rows: 102px 68px
                custom_fields:
                  titre_cuisine: |
                    [[[
                      const alert = states['input_boolean.lave_vaisselle_vide'].state === 'on';
                      const isOn = states['light.cuisine_2'].state === 'on';
                      const color = alert ? '#ff1744' : (isOn ? '#ffc107' : '#00e5ff');
                      const label = alert ? 'Vider Lave-Vaisselle' : 'Cuisine';
                      const fontSize = alert ? '17px' : '32px';
                      const letterSpacing = alert ? '1px' : '4px';
                      return `<div style="
                        display: flex;
                        align-items: center;
                        justify-content: center;
                        text-align: center;
                        height: 100%;
                        width: 100%;
                        font-family: Orbitron, sans-serif;
                        font-size: ${fontSize};
                        font-weight: 700;
                        letter-spacing: ${letterSpacing};
                        color: ${color};
                        text-shadow: 0 0 20px ${color}, 0 0 40px ${color}40;
                        white-space: nowrap;
                        padding: 0 8px;
                      ">${label}</div>`;
                    ]]]
                  ico_main:
                    card:
                      type: custom:button-card
                      entity: light.cuisine_2
                      show_name: false
                      show_state: false
                      icon: |
                        [[[
                          if (states['input_boolean.lave_vaisselle_vide'].state === 'on') return 'mdi:dishwasher-alert';
                          if (states['light.jarvis_power'].state === 'on') return 'cil:dishwasher-silverware';
                          return states['light.cuisine_2'].state === 'on' ? 'phu:kitchen' : 'hue:room-kitchen-off';
                        ]]]
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - height: 102px
                          - width: 95px
                          - border-right: |
                              [[[
                                if (states['input_boolean.lave_vaisselle_vide'].state === 'on') return '1px solid rgba(255,23,68,0.3)';
                                return states['light.cuisine_2'].state === 'on' ? '1px solid rgba(255,193,7,0.15)' : '1px solid rgba(0,229,255,0.12)';
                              ]]]
                          - border-radius: 0
                        icon:
                          - width: 75px
                          - height: 75px
                          - color: |
                              [[[
                                if (states['input_boolean.lave_vaisselle_vide'].state === 'on') return '#ff1744';
                                if (states['light.jarvis_power'].state === 'on') return 'red';
                                return states['light.cuisine_2'].state === 'on' ? '#ffc107' : '#00e5ff';
                              ]]]
                          - filter: |
                              [[[
                                if (states['input_boolean.lave_vaisselle_vide'].state === 'on') return 'drop-shadow(0 0 12px rgba(255,23,68,0.9))';
                                if (states['light.jarvis_power'].state === 'on') return 'drop-shadow(0 0 12px rgba(255,0,0,0.7))';
                                const c = states['light.cuisine_2'].state === 'on' ? 'rgba(255,193,7' : 'rgba(0,229,255';
                                return `drop-shadow(0 0 12px ${c},0.9)) drop-shadow(0 0 25px ${c},0.4))`;
                              ]]]
                          - animation: |
                              [[[
                                if (states['input_boolean.lave_vaisselle_vide'].state === 'on') return 'blink 2 ease infinite';
                                if (states['light.jarvis_power'].state === 'on') return 'rotating 5 alternate infinite';
                                return 'none';
                              ]]]
                  b_bas:
                    card:
                      type: custom:button-card
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - padding: 0 6px
                          - height: 68px
                          - display: flex
                          - align-items: flex-end
                          - overflow: visible
                        grid:
                          - grid-template-columns: repeat(6, 1fr)
                          - grid-template-areas: '"b1 b2 b3 b4 b5 b6"'
                          - gap: 4px
                          - align-items: flex-end
                      custom_fields:
                        b1:
                          card:
                            type: custom:button-card
                            entity: input_boolean.cuisine
                            show_name: false
                            show_state: false
                            icon: mdi:spotlight
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: call-service
                              service: input_boolean.toggle
                              service_data:
                                entity_id: input_boolean.cuisine
                            hold_action:
                              action: more-info
                              entity: light.cuisine
                        b2:
                          card:
                            type: custom:button-card
                            entity: input_boolean.bar
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return states['input_boolean.bar'].state ===
                              'on' ? 'mdi:ceiling-light-multiple' :
                              'mdi:ceiling-light-multiple-outline'; ]]]
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: call-service
                              service: input_boolean.toggle
                              service_data:
                                entity_id: input_boolean.bar
                            hold_action:
                              action: more-info
                              entity: light.bar
                        b3:
                          card:
                            type: custom:button-card
                            entity: sensor.fin_du_cycle_a
                            show_name: false
                            show_state: true
                            icon: |
                              [[[
                                if (states['sensor.jarvis_door']?.state === 'open') return 'mdi:dishwasher-alert';
                                if (states['binary_sensor.do_pf_cuisine']?.state === 'on') return 'mdi:window-open-variant';
                                if (states['input_boolean.lave_vaisselle_en_marche']?.state === 'on') return 'mdi:dishwasher-alert';
                                if (states['sensor.jarvis_etat']?.state === 'run') return 'cil:dishwasher-silverware';
                                return 'mdi:dishwasher';
                              ]]]
                            styles:
                              icon:
                                - display: block
                                - width: 22px
                                - color: |
                                    [[[
                                      if (states['sensor.jarvis_door']?.state === 'open' || states['binary_sensor.do_pf_cuisine']?.state === 'on') return 'rgba(255,23,68,0.9)';
                                      if (states['input_boolean.lave_vaisselle_en_marche']?.state === 'on' || states['sensor.jarvis_etat']?.state === 'run') return '#ffc107';
                                      return 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      if (states['sensor.jarvis_door']?.state === 'open' || states['binary_sensor.do_pf_cuisine']?.state === 'on') return 'drop-shadow(0 0 6px rgba(255,23,68,0.8))';
                                      if (states['input_boolean.lave_vaisselle_en_marche']?.state === 'on' || states['sensor.jarvis_etat']?.state === 'run') return 'drop-shadow(0 0 6px rgba(255,193,7,0.8))';
                                      return 'none';
                                    ]]]
                                - animation: |
                                    [[[
                                      if (states['sensor.jarvis_door']?.state === 'open') return 'blink 1 steps(1) infinite';
                                      if (states['binary_sensor.do_pf_cuisine']?.state === 'on' || states['input_boolean.lave_vaisselle_en_marche']?.state === 'on') return 'blink 2 ease infinite';
                                      return 'none';
                                    ]]]
                              state:
                                - display: |
                                    [[[
                                      const doorOpen = states['sensor.jarvis_door']?.state === 'open';
                                      const winOpen = states['binary_sensor.do_pf_cuisine']?.state === 'on';
                                      const armed = states['input_boolean.lave_vaisselle_en_marche']?.state === 'on';
                                      const running = states['sensor.jarvis_etat']?.state === 'run';
                                      return (!doorOpen && !winOpen && !armed && running) ? 'block' : 'none';
                                    ]]]
                                - color: '#ffc107'
                                - font-family: Orbitron, sans-serif
                                - font-size: 11px
                                - font-weight: 700
                                - text-shadow: 0 0 6px rgba(255,193,7,0.8)
                              card:
                                - background: |
                                    [[[
                                      if (states['sensor.jarvis_door']?.state === 'open' || states['binary_sensor.do_pf_cuisine']?.state === 'on') return 'rgba(255,23,68,0.1)';
                                      if (states['input_boolean.lave_vaisselle_en_marche']?.state === 'on' || states['sensor.jarvis_etat']?.state === 'run') return 'rgba(255,193,7,0.12)';
                                      return 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      if (states['sensor.jarvis_door']?.state === 'open' || states['binary_sensor.do_pf_cuisine']?.state === 'on') return '1px solid rgba(255,23,68,0.45)';
                                      if (states['input_boolean.lave_vaisselle_en_marche']?.state === 'on' || states['sensor.jarvis_etat']?.state === 'run') return '1px solid rgba(255,193,7,0.45)';
                                      return '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      if (states['sensor.jarvis_door']?.state === 'open' || states['binary_sensor.do_pf_cuisine']?.state === 'on') return '2px 4px 14px rgba(255,23,68,0.18), inset 0 -3px 0 #ff1744';
                                      if (states['input_boolean.lave_vaisselle_en_marche']?.state === 'on' || states['sensor.jarvis_etat']?.state === 'run') return '2px 4px 14px rgba(255,193,7,0.18), inset 0 -3px 0 #ffc107';
                                      return 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      if (states['sensor.jarvis_door']?.state === 'open' || states['binary_sensor.do_pf_cuisine']?.state === 'on' || states['input_boolean.lave_vaisselle_en_marche']?.state === 'on' || states['sensor.jarvis_etat']?.state === 'run') return '52px';
                                      return '42px';
                                    ]]]
                            tap_action:
                              action: call-service
                              service: input_boolean.turn_on
                              service_data:
                                entity_id: input_boolean.lave_vaisselle_en_marche
                              confirmation:
                                text: >-
                                  Jarvis est-il opérationnel pour le lancement à
                                  23h30 ?
                            hold_action:
                              action: fire-dom-event
                              browser_mod:
                                service: browser_mod.popup
                                data:
                                  title: JARVIS
                                  style: >
                                    --popup-background: rgba(0, 5, 15, 0.96);
                                    --popup-border-radius: 6px;
                                    --popup-border-color: rgba(0,229,255,0.22);
                                    --popup-box-shadow: 0 0 40px
                                    rgba(0,229,255,0.08); --ha-card-background:
                                    transparent; --mdc-theme-surface: rgba(0, 5,
                                    15, 0.96); --primary-text-color: #00e5ff;
                                    --secondary-text-color:
                                    rgba(0,229,255,0.55); --mdc-theme-primary:
                                    #00e5ff; --paper-item-icon-color: #00e5ff;
                                    backdrop-filter: blur(12px);
                                  content:
                                    type: custom:mushroom-select-card
                                    entity: select.jarvis_active_program
                                    icon: cil:dishwasher-silverware
                                    name: Activation d'un programme Jarvis
                                    layout: vertical
                                    secondary_info: none
                                    icon_color: white
                        b4:
                          card:
                            type: custom:button-card
                            entity: light.ptit_dej
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return states['light.ptit_dej'].state === 'on'
                              ? 'mdi:coffee-maker' : 'mdi:coffee-maker-outline';
                              ]]]
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                              - value: unavailable
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: call-service
                              service: light.toggle
                              service_data:
                                entity_id: light.ptit_dej
                        b5:
                          card:
                            type: custom:button-card
                            entity: light.micro_ondes
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return states['light.micro_ondes'].state ===
                              'on' ? 'mdi:microwave' : 'mdi:microwave-off'; ]]]
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                              - value: unavailable
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: call-service
                              service: light.toggle
                              service_data:
                                entity_id: light.micro_ondes
                        b6:
                          card:
                            type: custom:button-card
                            entity: light.thermomix
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return states['light.thermomix'].state ===
                              'on' ? 'mdi:blender' : 'mdi:blender-outline'; ]]]
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                              - value: unavailable
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: call-service
                              service: light.toggle
                              service_data:
                                entity_id: light.thermomix
                  DecoLines: |
                    [[[
                      const alert = states['input_boolean.lave_vaisselle_vide'].state === 'on';
                      const isOn = states['light.cuisine_2'].state === 'on';
                      const c = alert ? '#ff1744' : (isOn ? '#ffc107' : '#00e5ff');
                      const cOff = alert ? 'rgba(255,23,68,0.18)' : (isOn ? 'rgba(255,193,7,0.18)' : 'rgba(0,229,255,0.18)');
                      const divLine = alert ? 'rgba(255,23,68,0.15)' : (isOn ? 'rgba(255,193,7,0.15)' : 'rgba(0,229,255,0.12)');
                      const topLine = alert
                        ? 'linear-gradient(90deg,transparent,#ff1744 30%,#ff1744 70%,transparent)'
                        : (isOn
                          ? 'linear-gradient(90deg,transparent,#ffc107 30%,#ffc107 70%,transparent)'
                          : 'linear-gradient(90deg,transparent,#00e5ff 30%,#00e5ff 70%,transparent)');
                      return `
                        <style>
                          @keyframes rotating { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
                          @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }
                        </style>
                        <div style="position:absolute;top:0;left:0;right:0;height:1px;background:${topLine};opacity:0.65;pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;bottom:68px;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,${divLine},transparent);pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;top:-1px;left:-1px;width:12px;height:12px;border-top:2px solid ${c};border-left:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;top:-1px;right:-1px;width:12px;height:12px;border-top:2px solid ${c};border-right:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;left:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-left:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;right:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-right:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                      `;
                    ]]]
                double_tap_action:
                  action: call-service
                  service: input_boolean.turn_off
                  service_data:
                    entity_id: input_boolean.lave_vaisselle_vide
                hold_action:
                  action: navigate
                  navigation_path: /lovelace/cuisine
                tap_action:
                  action: fire-dom-event
                  honeycomb_menu:
                    autoclose: true
                    size: 210
                    filter_radius: 100
                    style:
                      - background: rgba(0,5,15,0.92)
                      - border: 1px solid rgba(0,229,255,0.25)
                      - box-shadow: 0 0 40px rgba(0,229,255,0.1)
                      - border-radius: 6px
                    buttons:
                      - entity: light.tablette_ha
                        icon: mdi:tablet
                        state:
                          - value: 'on'
                            styles:
                              icon:
                                - color: '#ffc107'
                                - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                              card:
                                - background: rgba(255,193,7,0.12)
                                - border: 1px solid rgba(255,193,7,0.4)
                                - box-shadow: 0 0 15px rgba(255,193,7,0.2)
                          - value: 'off'
                            styles:
                              icon:
                                - color: rgba(0,229,255,0.35)
                              card:
                                - background: rgba(0,10,22,0.85)
                                - border: 1px solid rgba(0,229,255,0.2)
                        tap_action:
                          action: call-service
                          service: light.toggle
                          service_data:
                            entity_id: light.tablette_ha
                      - entity: light.baymax
                        icon: mdi:robot-vacuum
                        state:
                          - value: 'on'
                            styles:
                              icon:
                                - color: '#ffc107'
                                - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                              card:
                                - background: rgba(255,193,7,0.12)
                                - border: 1px solid rgba(255,193,7,0.4)
                                - box-shadow: 0 0 15px rgba(255,193,7,0.2)
                          - value: 'off'
                            styles:
                              icon:
                                - color: rgba(0,229,255,0.35)
                              card:
                                - background: rgba(0,10,22,0.85)
                                - border: 1px solid rgba(0,229,255,0.2)
                        tap_action:
                          action: call-service
                          service: light.toggle
                          service_data:
                            entity_id: light.baymax
                      - icon: mdi:vanity-light
                        entity: input_boolean.spots_cuisine
                        state:
                          - value: 'on'
                            styles:
                              icon:
                                - color: '#ffc107'
                                - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                              card:
                                - background: rgba(255,193,7,0.12)
                                - border: 1px solid rgba(255,193,7,0.4)
                                - box-shadow: 0 0 15px rgba(255,193,7,0.2)
                          - value: 'off'
                            styles:
                              icon:
                                - color: rgba(0,229,255,0.35)
                              card:
                                - background: rgba(0,10,22,0.85)
                                - border: 1px solid rgba(0,229,255,0.2)
                        tap_action:
                          action: call-service
                          service: input_boolean.toggle
                          service_data:
                            entity_id: input_boolean.spots_cuisine
                      - entity: cover.volet_roulant_cuisine
                        icon: mdi:window-shutter-open
                        state:
                          - value: open
                            styles:
                              icon:
                                - color: '#ffc107'
                                - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                              card:
                                - background: rgba(255,193,7,0.12)
                                - border: 1px solid rgba(255,193,7,0.4)
                                - box-shadow: 0 0 15px rgba(255,193,7,0.2)
                          - value: closed
                            icon: mdi:window-shutter
                            styles:
                              icon:
                                - color: rgba(0,229,255,0.35)
                              card:
                                - background: rgba(0,10,22,0.85)
                                - border: 1px solid rgba(0,229,255,0.2)
                        tap_action:
                          action: call-service
                          service: cover.toggle
                          service_data:
                            entity_id: cover.volet_roulant_cuisine
                        hold_action:
                          action: call-service
                          service: cover.stop_cover
                          service_data:
                            entity_id: cover.volet_roulant_cuisine
                      - entity: light.micro_ondes
                        icon: mdi:microwave
                        state:
                          - value: 'on'
                            styles:
                              icon:
                                - color: '#ffc107'
                                - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                              card:
                                - background: rgba(255,193,7,0.12)
                                - border: 1px solid rgba(255,193,7,0.4)
                                - box-shadow: 0 0 15px rgba(255,193,7,0.2)
                          - value: 'off'
                            icon: mdi:microwave-off
                            styles:
                              icon:
                                - color: rgba(0,229,255,0.35)
                              card:
                                - background: rgba(0,10,22,0.85)
                                - border: 1px solid rgba(0,229,255,0.2)
                        tap_action:
                          action: call-service
                          service: light.toggle
                          service_data:
                            entity_id: light.micro_ondes
                      - entity: sensor.temperature_frigo
                        show_state: true
                        show_icon: false
                        show_name: false
                        styles:
                          state:
                            - color: '#00e5ff'
                            - font-family: Orbitron, sans-serif
                            - font-size: 13px
                            - filter: drop-shadow(0 0 6px rgba(0,229,255,0.6))
                          card:
                            - background: rgba(0,229,255,0.08)
                            - border: 1px solid rgba(0,229,255,0.3)
                            - box-shadow: 0 0 15px rgba(0,229,255,0.1)
              - type: custom:button-card
                entity: sensor.etat_de_baymax
                show_name: false
                show_state: false
                show_icon: false
                styles:
                  card:
                    - height: 170px
                    - border-radius: 6px
                    - background: |
                        [[[
                          const s = states['sensor.etat_de_baymax'].state;
                          if (s === 'Nettoyage en Cours') return 'rgba(0,229,255,0.03)';
                          if (s === 'Retour à la Base') return 'rgba(255,193,7,0.03)';
                          if (s === 'En Charge') return 'rgba(255,193,7,0.03)';
                          return 'rgba(0,229,255,0.03)';
                        ]]]
                    - border: |
                        [[[
                          const s = states['sensor.etat_de_baymax'].state;
                          if (s === 'Retour à la Base' || s === 'En Charge') return '1px solid rgba(255,193,7,0.22)';
                          return '1px solid rgba(0,229,255,0.2)';
                        ]]]
                    - box-shadow: |
                        [[[
                          const s = states['sensor.etat_de_baymax'].state;
                          if (s === 'Retour à la Base' || s === 'En Charge') return '0 0 40px rgba(255,193,7,0.07)';
                          return '0 0 40px rgba(0,229,255,0.06)';
                        ]]]
                    - padding: 0
                    - overflow: visible
                    - position: relative
                  grid:
                    - grid-template-areas: '"ico_main titre_baymax" "b_bas b_bas"'
                    - grid-template-columns: 95px 1fr
                    - grid-template-rows: 102px 68px
                custom_fields:
                  titre_baymax: |
                    [[[
                      const s = states['sensor.etat_de_baymax'].state;
                      const isWarm = s === 'Retour à la Base' || s === 'En Charge';
                      const color = isWarm ? '#ffc107' : '#00e5ff';
                      const labels = {
                        'En Charge': 'En Charge',
                        'En Pause': 'En Pause',
                        'Retour à la Base': 'Retour Base',
                        'Nettoyage en Cours': 'Nettoyage',
                        'Éteint': 'Baymax'
                      };
                      const label = labels[s] || 'Baymax';
                      const blinkStates = ['Retour à la Base', 'Nettoyage en Cours'];
                      const animation = blinkStates.includes(s) ? 'blink 3 ease infinite' : 'none';
                      return `<div style="
                        display: flex;
                        align-items: center;
                        justify-content: center;
                        text-align: center;
                        height: 100%;
                        width: 100%;
                        font-family: Orbitron, sans-serif;
                        font-size: 32px;
                        font-weight: 700;
                        letter-spacing: 3px;
                        color: ${color};
                        text-shadow: 0 0 20px ${color}, 0 0 40px ${color}40;
                        white-space: nowrap;
                        animation: ${animation};
                      ">${label}</div>`;
                    ]]]
                  ico_main: |
                    [[[
                      const s = states['sensor.etat_de_baymax'].state;

                      if (s === 'Nettoyage en Cours') {
                        return `
                          <style>
                            @keyframes baymax-balade {
                              0%   { offset-distance: 0%; }
                              100% { offset-distance: 100%; }
                            }
                            @keyframes baymax-trail-fade {
                              0%   { opacity: 0.7; }
                              100% { opacity: 0; }
                            }
                          </style>
                          <div style="
                            position: relative;
                            width: 95px;
                            height: 102px;
                            border-right: 1px solid rgba(0,229,255,0.12);
                            overflow: hidden;
                            background:
                              repeating-linear-gradient(0deg, rgba(0,229,255,0.04) 0 1px, transparent 1px 14px),
                              repeating-linear-gradient(90deg, rgba(0,229,255,0.04) 0 1px, transparent 1px 14px);
                          ">
                            <svg width="95" height="102" style="position:absolute;top:0;left:0;">
                              <path id="baymax-path" d="M 15,20 L 70,15 L 80,50 L 25,60 L 12,85 L 60,90"
                                fill="none" stroke="rgba(0,229,255,0.15)" stroke-width="1" stroke-dasharray="3,3"/>
                            </svg>
                            <div style="
                              position: absolute;
                              width: 7px; height: 7px;
                              border-radius: 50%;
                              background: #00e5ff;
                              box-shadow: 0 0 8px #00e5ff, 0 0 16px rgba(0,229,255,0.6);
                              offset-path: path('M 15,20 L 70,15 L 80,50 L 25,60 L 12,85 L 60,90');
                              animation: baymax-balade 9s ease-in-out infinite alternate;
                            "></div>
                            <ha-icon icon="mdi:robot-vacuum" style="
                              position: absolute;
                              bottom: 4px; right: 4px;
                              width: 18px; height: 18px;
                              color: rgba(0,229,255,0.5);
                            "></ha-icon>
                          </div>
                        `;
                      }

                      const icons = {
                        'En Charge': 'mdi:battery-charging',
                        'En Pause': 'mdi:pause',
                        'Retour à la Base': 'mdi:robot-vacuum-alert',
                        'Éteint': 'mdi:robot-vacuum-off'
                      };
                      const icon = icons[s] || 'mdi:robot-vacuum';
                      const isWarm = s === 'Retour à la Base' || s === 'En Charge' || s === 'En Pause';
                      const color = s === 'Éteint' ? '#00e5ff' : (isWarm ? '#ffc107' : '#00e5ff');
                      const cGlow = isWarm ? 'rgba(255,193,7' : 'rgba(0,229,255';
                      const anim = s === 'Retour à la Base' ? 'rotating 3s linear infinite' : 'none';

                      return `
                        <style>@keyframes rotating { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }</style>
                        <div style="
                          display:flex; align-items:center; justify-content:center;
                          width:95px; height:102px;
                          border-right: 1px solid ${isWarm ? 'rgba(255,193,7,0.15)' : 'rgba(0,229,255,0.12)'};
                        ">
                          <ha-icon icon="${icon}" style="
                            width:70px; height:70px; color:${color};
                            filter: drop-shadow(0 0 12px ${cGlow},0.9)) drop-shadow(0 0 25px ${cGlow},0.4));
                            animation: ${anim};
                          "></ha-icon>
                        </div>
                      `;
                    ]]]
                  b_bas:
                    card:
                      type: custom:button-card
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - padding: 0 6px
                          - height: 68px
                          - display: flex
                          - align-items: flex-end
                          - overflow: visible
                        grid:
                          - grid-template-columns: repeat(6, 1fr)
                          - grid-template-areas: '"b1 b2 b3 b4 b5 b6"'
                          - gap: 4px
                          - align-items: flex-end
                      custom_fields:
                        b1:
                          card:
                            type: custom:button-card
                            entity: light.baymax
                            show_name: false
                            show_state: false
                            icon: mdi:robot-vacuum
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: toggle
                        b2:
                          card:
                            type: custom:button-card
                            entity: sensor.roidmi_v60_f2ec_battery_level
                            show_name: false
                            show_state: true
                            show_icon: true
                            icon: mdi:battery-high
                            styles:
                              card:
                                - background: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'En Charge' ? 'rgba(57,255,20,0.12)' : 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'En Charge' ? '1px solid rgba(57,255,20,0.45)' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - border-radius: 0 0 8px 0
                                - box-shadow: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'En Charge' ? '2px 4px 14px rgba(57,255,20,0.18), inset 0 -3px 0 #39ff14' : 'none';
                                    ]]]
                                - height: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'En Charge' ? '52px' : '42px';
                                    ]]]
                                - position: relative
                              icon:
                                - width: 22px
                                - color: |
                                    [[[
                                      if (states['sensor.etat_de_baymax']?.state === 'En Charge') return '#39ff14';
                                      let val = states['vacuum.baymax']?.attributes?.battery_level;
                                      if (!val) return 'rgba(0,229,255,0.35)';
                                      let hue = Math.min(120, val * 1.5);
                                      return `hsl(${hue}, 90%, 55%)`;
                                    ]]]
                                - filter: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'En Charge' ? 'drop-shadow(0 0 6px rgba(57,255,20,0.8))' : 'none';
                                    ]]]
                              state:
                                - font-size: 9px
                                - font-weight: bold
                                - color: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'En Charge' ? '#39ff14' : '#00e5ff';
                                    ]]]
                                - position: absolute
                                - bottom: 3px
                                - left: 50%
                                - transform: translateX(-50%)
                                - text-shadow: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'En Charge' ? '0 0 4px rgba(57,255,20,0.8)' : '0 0 4px rgba(0,229,255,0.8)';
                                    ]]]
                            state_display: |
                              [[[
                                return states['vacuum.baymax']?.attributes?.battery_level + '%';
                              ]]]
                        b3:
                          card:
                            type: custom:button-card
                            entity: button.roidmi_v60_f2ec_start_sweep
                            show_name: false
                            show_state: false
                            icon: mdi:play-circle
                            styles:
                              icon:
                                - width: 22px
                                - color: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'Nettoyage en Cours' ? '#ffc107' : 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'Nettoyage en Cours' ? 'drop-shadow(0 0 6px rgba(255,193,7,0.8))' : 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'Nettoyage en Cours' ? 'rgba(255,193,7,0.12)' : 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'Nettoyage en Cours' ? '1px solid rgba(255,193,7,0.45)' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - border-radius: 0 0 8px 0
                                - box-shadow: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'Nettoyage en Cours' ? '2px 4px 14px rgba(255,193,7,0.18), inset 0 -3px 0 #ffc107' : 'none';
                                    ]]]
                                - height: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'Nettoyage en Cours' ? '52px' : '42px';
                                    ]]]
                            tap_action:
                              action: call-service
                              service: button.press
                              service_data:
                                entity_id: button.roidmi_v60_f2ec_start_sweep
                        b4:
                          card:
                            type: custom:button-card
                            entity: button.roidmi_v60_f2ec_start_charge
                            show_name: false
                            show_state: false
                            icon: mdi:home-import-outline
                            styles:
                              icon:
                                - width: 22px
                                - color: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'Retour à la Base' ? '#ffc107' : 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'Retour à la Base' ? 'drop-shadow(0 0 6px rgba(255,193,7,0.8))' : 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'Retour à la Base' ? 'rgba(255,193,7,0.12)' : 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'Retour à la Base' ? '1px solid rgba(255,193,7,0.45)' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - border-radius: 0 0 8px 0
                                - box-shadow: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'Retour à la Base' ? '2px 4px 14px rgba(255,193,7,0.18), inset 0 -3px 0 #ffc107' : 'none';
                                    ]]]
                                - height: |
                                    [[[
                                      return states['sensor.etat_de_baymax']?.state === 'Retour à la Base' ? '52px' : '42px';
                                    ]]]
                            tap_action:
                              action: call-service
                              service: button.press
                              service_data:
                                entity_id: button.roidmi_v60_f2ec_start_charge
                        b5:
                          card:
                            type: custom:button-card
                            entity: select.roidmi_v60_f2ec_mode
                            show_name: false
                            show_state: false
                            icon: mdi:fan
                            styles:
                              icon:
                                - color: '#00e5ff'
                                - width: 22px
                                - filter: drop-shadow(0 0 4px rgba(0,229,255,0.6))
                              card:
                                - background: rgba(0,10,22,0.85)
                                - border: 1px solid rgba(0,229,255,0.2)
                                - border-top: none
                                - border-left: none
                                - border-radius: 0 0 8px 0
                                - height: 42px
                            tap_action:
                              action: more-info
                        b6:
                          card:
                            type: custom:button-card
                            entity: select.roidmi_v60_f2ec_water_level
                            show_name: false
                            show_state: false
                            icon: mdi:water-opacity
                            styles:
                              icon:
                                - color: '#00e5ff'
                                - width: 22px
                                - filter: drop-shadow(0 0 4px rgba(0,229,255,0.6))
                              card:
                                - background: rgba(0,10,22,0.85)
                                - border: 1px solid rgba(0,229,255,0.2)
                                - border-top: none
                                - border-left: none
                                - border-radius: 0 0 8px 0
                                - height: 42px
                            tap_action:
                              action: more-info
                  DecoLines: |
                    [[[
                      const s = states['sensor.etat_de_baymax'].state;
                      const isWarm = s === 'Retour à la Base' || s === 'En Charge';
                      const c = isWarm ? '#ffc107' : '#00e5ff';
                      const cOff = isWarm ? 'rgba(255,193,7,0.18)' : 'rgba(0,229,255,0.18)';
                      const divLine = isWarm ? 'rgba(255,193,7,0.15)' : 'rgba(0,229,255,0.12)';
                      const topLine = isWarm
                        ? 'linear-gradient(90deg,transparent,#ffc107 30%,#ffc107 70%,transparent)'
                        : 'linear-gradient(90deg,transparent,#00e5ff 30%,#00e5ff 70%,transparent)';
                      return `
                        <style>
                          @keyframes rotating { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
                          @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }
                        </style>
                        <div style="position:absolute;top:0;left:0;right:0;height:1px;background:${topLine};opacity:0.65;pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;bottom:68px;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,${divLine},transparent);pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;top:-1px;left:-1px;width:12px;height:12px;border-top:2px solid ${c};border-left:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;top:-1px;right:-1px;width:12px;height:12px;border-top:2px solid ${c};border-right:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;left:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-left:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;right:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-right:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                      `;
                    ]]]
                tap_action:
                  action: navigate
                  navigation_path: /lovelace/baymax
            columns: 1
            grid_options:
              columns: full
        column_span: 1
      - type: grid
        cards:
          - square: false
            type: grid
            cards:
              - type: custom:button-card
                entity: binary_sensor.do_porte_de_garage
                show_name: false
                show_state: false
                show_icon: false
                tap_action:
                  action: navigate
                  navigation_path: /lovelace/autres
                styles:
                  card:
                    - height: 170px
                    - border-radius: 6px
                    - background: |
                        [[[
                          if (states['binary_sensor.camera_garage_cell_motion_detection']?.state === 'on') return 'rgba(255,23,68,0.05)';
                          return states['binary_sensor.do_porte_de_garage'].state === 'on' ? 'rgba(255,23,68,0.03)' : 'rgba(0,229,255,0.03)';
                        ]]]
                    - background-size: cover
                    - border: |
                        [[[
                          if (states['binary_sensor.camera_garage_cell_motion_detection']?.state === 'on') return '1px solid rgba(255,23,68,0.6)';
                          return states['binary_sensor.do_porte_de_garage'].state === 'on' ? '1px solid rgba(255,23,68,0.45)' : '1px solid rgba(0,229,255,0.2)';
                        ]]]
                    - box-shadow: |
                        [[[
                          if (states['binary_sensor.camera_garage_cell_motion_detection']?.state === 'on') return '0 0 40px rgba(255,23,68,0.25)';
                          return states['binary_sensor.do_porte_de_garage'].state === 'on' ? '0 0 40px rgba(255,23,68,0.15)' : '0 0 40px rgba(0,229,255,0.06)';
                        ]]]
                    - padding: 0
                    - overflow: hidden
                    - position: relative
                  grid:
                    - grid-template-areas: >-
                        "horl horl heure heure heure heure" "horl horl heure
                        heure heure heure" "b_bas b_bas b_bas b_bas b_bas b_bas"
                    - grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr
                    - grid-template-rows: 51px 51px 68px
                  custom_fields:
                    camBg:
                      - position: absolute
                      - top: 0
                      - left: 0
                      - width: 100%
                      - height: 100%
                      - z-index: 1
                      - pointer-events: none
                      - overflow: hidden
                      - border-radius: 6px
                      - opacity: |
                          [[[
                            return states['binary_sensor.do_porte_de_garage'].state === 'on' ? 1 : 0;
                          ]]]
                      - transition: opacity 0.6s ease
                    camOverlay:
                      - position: absolute
                      - top: 0
                      - left: 0
                      - width: 100%
                      - height: 100%
                      - z-index: 2
                      - pointer-events: none
                    horl:
                      - z-index: 3
                      - align-self: center
                      - justify-self: center
                    heure:
                      - justify-self: center
                      - align-self: center
                      - z-index: 3
                    b_bas:
                      - justify-self: stretch
                      - align-self: stretch
                      - z-index: 3
                    DecoLines:
                      - position: absolute
                      - top: 0
                      - left: 0
                      - width: 100%
                      - height: 100%
                      - pointer-events: none
                      - z-index: 5
                custom_fields:
                  camBg:
                    card:
                      type: picture-glance
                      camera_image: camera.camera_garage
                      camera_view: live
                      entities: []
                      card_mod:
                        style: |
                          ha-card {
                            background: transparent !important;
                            border: none !important;
                            box-shadow: none !important;
                            border-radius: 6px !important;
                          }
                          .box { display: none !important; }
                          img, video {
                            filter: brightness(0.55) saturate(1.1);
                            object-fit: cover;
                            width: 100%;
                            height: 100%;
                          }
                  camOverlay: |
                    [[[
                      const open = states['binary_sensor.do_porte_de_garage'].state === 'on';
                      if (!open) return '';
                      const motion = states['binary_sensor.camera_garage_cell_motion_detection']?.state === 'on';
                      const tint = motion ? 'rgba(255,23,68,0.12)' : '';
                      return `
                        <div style="position:absolute;top:0;left:0;width:100%;height:100%;background:linear-gradient(180deg, rgba(0,10,18,0.25) 0%, rgba(0,10,18,0.05) 45%, rgba(0,10,18,0.75) 100%);"></div>
                        ${tint ? `<div style="position:absolute;top:0;left:0;width:100%;height:100%;background:${tint};"></div>` : ''}
                      `;
                    ]]]
                  horl: |
                    [[[
                      const motion = states['binary_sensor.camera_garage_cell_motion_detection']?.state === 'on';
                      const color = motion ? '#ff1744' : '#00e5ff';
                      const colorFaded = motion ? 'rgba(255,23,68,0.25)' : 'rgba(0,229,255,0.25)';
                      const colorMid = motion ? 'rgba(255,23,68,0.5)' : 'rgba(0,229,255,0.5)';

                      if (states['sensor.prise_machine_a_laver_active_power']?.state > 5) {
                        return `<ha-icon icon="mdi:washing-machine" style="
                          width:55px;height:55px;color:${color};
                          filter:drop-shadow(0 0 8px ${color});
                          animation:rotating 3 linear alternate infinite;
                        "></ha-icon>`;
                      }

                      const date = new Date();
                      const h = date.getHours() % 12;
                      const m = date.getMinutes();
                      const s = date.getSeconds();
                      const hrDeg  = h * 30 + m * 0.5;
                      const minDeg = m * 6 + s * 0.1;
                      const secDeg = s * 6;

                      let ticks = '';
                      for (let i = 0; i < 60; i++) {
                        const angle = i * 6;
                        const isMain = i % 5 === 0;
                        const r1 = isMain ? 34 : 36;
                        const r2 = 38;
                        const sw = isMain ? 1.5 : 0.8;
                        const op = isMain ? 0.8 : 0.35;
                        const x1 = 50 + r1 * Math.sin(angle * Math.PI / 180);
                        const y1 = 50 - r1 * Math.cos(angle * Math.PI / 180);
                        const x2 = 50 + r2 * Math.sin(angle * Math.PI / 180);
                        const y2 = 50 - r2 * Math.cos(angle * Math.PI / 180);
                        ticks += `<line x1="${x1.toFixed(1)}" y1="${y1.toFixed(1)}" x2="${x2.toFixed(1)}" y2="${y2.toFixed(1)}" stroke="${color}" stroke-width="${sw}" opacity="${op}"/>`;
                      }

                      return `
                        <svg viewBox="0 0 100 100" style="width:95px;height:95px;display:block;margin:auto;">
                          <circle cx="50" cy="50" r="40" stroke="${color}" stroke-width="1.5" fill="none" opacity="0.4"/>
                          <circle cx="50" cy="50" r="32" stroke="${colorFaded}" stroke-width="0.5" fill="none"/>
                          ${ticks}
                          <circle cx="50" cy="22" r="9" stroke="${colorMid}" stroke-width="0.8" fill="none"/>
                          <line x1="50" y1="22"
                                x2="${(50 + 7 * Math.sin(secDeg * Math.PI / 180)).toFixed(2)}"
                                y2="${(22 - 7 * Math.cos(secDeg * Math.PI / 180)).toFixed(2)}"
                                stroke="${color}" stroke-width="0.8" stroke-linecap="round"/>
                          <line x1="50" y1="50"
                                x2="${(50 + 18 * Math.sin(hrDeg * Math.PI / 180)).toFixed(2)}"
                                y2="${(50 - 18 * Math.cos(hrDeg * Math.PI / 180)).toFixed(2)}"
                                stroke="${color}" stroke-width="3" stroke-linecap="round"
                                style="filter:drop-shadow(0 0 3px ${color})"/>
                          <line x1="50" y1="50"
                                x2="${(50 + 26 * Math.sin(minDeg * Math.PI / 180)).toFixed(2)}"
                                y2="${(50 - 26 * Math.cos(minDeg * Math.PI / 180)).toFixed(2)}"
                                stroke="${color}" stroke-width="2" stroke-linecap="round"
                                style="filter:drop-shadow(0 0 3px ${color})"/>
                          <circle cx="50" cy="50" r="2.5" fill="${color}" style="filter:drop-shadow(0 0 4px ${color})"/>
                          <circle cx="50" cy="50" r="1" fill="black"/>
                        </svg>
                      `;
                    ]]]
                  heure: |
                    [[[
                      const motion = states['binary_sensor.camera_garage_cell_motion_detection']?.state === 'on';
                      const color = motion ? '#ff1744' : '#00e5ff';
                      return `
                        <style>
                          @keyframes pulseTime { 0%,100%{transform:scale(1);opacity:0.85} 50%{transform:scale(1.05);opacity:1} }
                        </style>
                        <span style="
                          font-family: Orbitron, sans-serif;
                          font-size: 32px;
                          font-weight: 700;
                          color: ${color};
                          text-shadow: 0 0 20px ${color}, 0 0 40px ${color}40;
                          animation: pulseTime 2 ease-in-out infinite;
                          display: inline-block;
                          letter-spacing: 2px;
                        ">${states['sensor.heure'].state}</span>
                      `;
                    ]]]
                  b_bas:
                    card:
                      type: custom:button-card
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - padding: 0 6px
                          - height: 68px
                          - display: flex
                          - align-items: flex-end
                          - overflow: visible
                        grid:
                          - grid-template-columns: repeat(6, 1fr)
                          - grid-template-areas: '"b1 b2 b3 b4 b5 b6"'
                          - gap: 4px
                          - align-items: flex-end
                      custom_fields:
                        b1:
                          card:
                            type: custom:button-card
                            entity: switch.prise_machine_a_laver
                            show_name: false
                            show_state: false
                            icon: |
                              [[[
                                if (states["light.chauffage_buanderie"].state === "on") return "mdi:heat-wave";
                                return states["switch.prise_machine_a_laver"].state === "on" ? "mdi:washing-machine" : "mdi:washing-machine-off";
                              ]]]
                            styles:
                              icon:
                                - width: 20px
                                - color: |
                                    [[[
                                      if (states["light.chauffage_buanderie"].state === "on") return "rgba(255,23,68,0.9)";
                                      return states["switch.prise_machine_a_laver"].state === "on" ? "#ffc107" : "rgba(0,229,255,0.35)";
                                    ]]]
                                - filter: |
                                    [[[
                                      if (states["light.chauffage_buanderie"].state === "on") return "drop-shadow(0 0 5px rgba(255,23,68,0.8))";
                                      if (states["switch.prise_machine_a_laver"].state === "on") return "drop-shadow(0 0 5px rgba(255,193,7,0.8))";
                                      return "none";
                                    ]]]
                                - animation: |
                                    [[[
                                      return states["light.chauffage_buanderie"].state === "on" ? "blink 2 ease infinite" : "none";
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      if (states["light.chauffage_buanderie"].state === "on") return "rgba(255,23,68,0.1)";
                                      return states["switch.prise_machine_a_laver"].state === "on" ? "rgba(255,193,7,0.12)" : "rgba(0,10,22,0.85)";
                                    ]]]
                                - border: |
                                    [[[
                                      if (states["light.chauffage_buanderie"].state === "on") return "1px solid rgba(255,23,68,0.4)";
                                      return states["switch.prise_machine_a_laver"].state === "on" ? "1px solid rgba(255,193,7,0.45)" : "1px solid rgba(0,229,255,0.13)";
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      return states["switch.prise_machine_a_laver"].state === "on" ? "2px 4px 14px rgba(255,193,7,0.18), inset 0 -3px 0 #ffc107" : "none";
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      return (states["switch.prise_machine_a_laver"].state === "on" || states["light.chauffage_buanderie"].state === "on") ? "52px" : "42px";
                                    ]]]
                            tap_action:
                              action: call-service
                              service: switch.toggle
                              service_data:
                                entity_id: switch.prise_machine_a_laver
                        b2:
                          card:
                            type: custom:button-card
                            show_name: false
                            show_state: false
                            icon: |
                              [[[
                                if (states['sensor.statut_bb_8'].state === 'En tonte') return 'mdi:robot-mower';
                                if (states['light.pompe_piscine'].state === 'on') return 'mdi:water-pump';
                                if (states['light.pompe_piscine'].state === 'off' && states['light.prise_bb_8'].state === 'off' && states['light.prise_garage'].state === 'on') return 'mdi:string-lights';
                                return states['light.prise_garage'].state === 'on' ? 'mdi:tree' : 'mdi:tree-outline';
                              ]]]
                            extra_styles: >
                              @keyframes move-mower {
                              0%,100%{transform:translateX(-3px)}
                              50%{transform:translateX(3px)} }

                              @keyframes pulse-pump {
                              0%,100%{transform:scale(1);opacity:1}
                              50%{transform:scale(1.15);opacity:0.7} }

                              @keyframes christmas-flash {
                              0%{filter:hue-rotate(0deg);opacity:1}
                              25%{opacity:0.5}
                              50%{filter:hue-rotate(180deg);opacity:1}
                              75%{opacity:0.5}
                              100%{filter:hue-rotate(360deg);opacity:1} }
                            styles:
                              icon:
                                - width: 20px
                                - color: |
                                    [[[
                                      if (states['sensor.statut_bb_8'].state === 'En tonte') return 'limegreen';
                                      if (states['light.pompe_piscine'].state === 'on') return '#00bfff';
                                      if (states['light.pompe_piscine'].state === 'off' && states['light.prise_bb_8'].state === 'off' && states['light.prise_garage'].state === 'on') return '#ff1744';
                                      return states['light.prise_garage'].state === 'on' ? '#ffc107' : 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      if (states['sensor.statut_bb_8'].state === 'En tonte') return 'drop-shadow(0 0 5px limegreen)';
                                      if (states['light.pompe_piscine'].state === 'on') return 'drop-shadow(0 0 5px #00bfff)';
                                      if (states['light.prise_garage'].state === 'on') return 'drop-shadow(0 0 5px rgba(255,193,7,0.8))';
                                      return 'none';
                                    ]]]
                                - animation: |
                                    [[[
                                      if (states['sensor.statut_bb_8'].state === 'En tonte') return 'move-mower 2 linear infinite';
                                      if (states['light.pompe_piscine'].state === 'on') return 'pulse-pump 1.5 ease-in-out infinite';
                                      if (states['light.pompe_piscine'].state === 'off' && states['light.prise_bb_8'].state === 'off' && states['light.prise_garage'].state === 'on') return 'christmas-flash 2 linear infinite';
                                      return 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      if (states['light.prise_garage'].state === 'on' || states['sensor.statut_bb_8'].state === 'En tonte' || states['light.pompe_piscine'].state === 'on') return 'rgba(255,193,7,0.12)';
                                      return 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      if (states['light.prise_garage'].state === 'on' || states['sensor.statut_bb_8'].state === 'En tonte' || states['light.pompe_piscine'].state === 'on') return '1px solid rgba(255,193,7,0.45)';
                                      return '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      return states['light.prise_garage'].state === 'on' ? '2px 4px 14px rgba(255,193,7,0.18), inset 0 -3px 0 #ffc107' : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      return (states['light.prise_garage'].state === 'on' || states['sensor.statut_bb_8'].state === 'En tonte' || states['light.pompe_piscine'].state === 'on') ? '52px' : '42px';
                                    ]]]
                            tap_action:
                              action: call-service
                              service: light.toggle
                              service_data:
                                entity_id: light.prise_garage
                        b3:
                          card:
                            type: custom:button-card
                            show_name: false
                            show_state: false
                            icon: |
                              [[[
                                return states['binary_sensor.do_porte_de_garage'].state === 'on' ? 'mdi:garage-open-variant' : 'mdi:garage-variant-lock';
                              ]]]
                            styles:
                              icon:
                                - width: 20px
                                - color: |
                                    [[[
                                      return states['binary_sensor.do_porte_de_garage'].state === 'on' ? 'rgba(255,23,68,0.9)' : 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      return states['binary_sensor.do_porte_de_garage'].state === 'on' ? 'drop-shadow(0 0 5px rgba(255,23,68,0.8))' : 'none';
                                    ]]]
                                - animation: |
                                    [[[
                                      return states['binary_sensor.do_porte_de_garage'].state === 'on' ? 'blink 2 ease infinite' : 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      return states['binary_sensor.do_porte_de_garage'].state === 'on' ? 'rgba(255,23,68,0.1)' : 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      return states['binary_sensor.do_porte_de_garage'].state === 'on' ? '1px solid rgba(255,23,68,0.4)' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      return states['binary_sensor.do_porte_de_garage'].state === 'on' ? '2px 4px 14px rgba(255,23,68,0.18), inset 0 -3px 0 #ff1744' : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      return states['binary_sensor.do_porte_de_garage'].state === 'on' ? '52px' : '42px';
                                    ]]]
                            tap_action:
                              action: call-service
                              service: button.press
                              service_data:
                                entity_id: button.garage
                              confirmation:
                                text: |
                                  [[[
                                    return states['binary_sensor.do_porte_de_garage'].state === 'on' ? 'Fermer la Porte du Garage ?' : 'Ouvrir la Porte du Garage ?';
                                  ]]]
                        b4:
                          card:
                            type: custom:button-card
                            show_name: false
                            show_state: false
                            icon: mdi:gate
                            styles:
                              icon:
                                - width: 20px
                                - color: rgba(0,229,255,0.35)
                              card:
                                - background: rgba(0,10,22,0.85)
                                - border: 1px solid rgba(0,229,255,0.13)
                                - border-top: none
                                - border-left: none
                                - box-shadow: none
                                - border-radius: 0 0 8px 0
                                - height: 42px
                            tap_action:
                              action: fire-dom-event
                              browser_mod:
                                service: browser_mod.popup
                                data:
                                  title: PORTAIL
                                  style: >
                                    --popup-background: rgba(0, 5, 15, 0.96);
                                    --popup-border-radius: 6px;
                                    --popup-border-color: rgba(0,229,255,0.22);
                                    --popup-box-shadow: 0 0 40px
                                    rgba(0,229,255,0.08); --ha-card-background:
                                    transparent; --mdc-theme-surface: rgba(0, 5,
                                    15, 0.96); --primary-text-color: #00e5ff;
                                    --mdc-theme-primary: #00e5ff;
                                    backdrop-filter: blur(12px);
                                  content:
                                    type: horizontal-stack
                                    cards:
                                      - type: custom:button-card
                                        entity: button.portail_voiture
                                        name: Voiture
                                        icon: mdi:car
                                        styles:
                                          card:
                                            - height: 100px
                                            - border-radius: 6px
                                            - background: rgba(0,229,255,0.08)
                                            - border: 1px solid rgba(0,229,255,0.3)
                                          name:
                                            - color: '#00e5ff'
                                            - font-family: Orbitron, sans-serif
                                            - font-size: 11px
                                          icon:
                                            - color: '#00e5ff'
                                            - filter: drop-shadow(0 0 6px rgba(0,229,255,0.8))
                                        tap_action:
                                          action: call-service
                                          service: button.press
                                          service_data:
                                            entity_id: button.portail_voiture
                                      - type: custom:button-card
                                        entity: button.portail_pieton
                                        name: Piéton
                                        icon: mdi:walk
                                        styles:
                                          card:
                                            - height: 100px
                                            - border-radius: 6px
                                            - background: rgba(255,193,7,0.08)
                                            - border: 1px solid rgba(255,193,7,0.3)
                                          name:
                                            - color: '#ffc107'
                                            - font-family: Orbitron, sans-serif
                                            - font-size: 11px
                                          icon:
                                            - color: '#ffc107'
                                            - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                        tap_action:
                                          action: call-service
                                          service: button.press
                                          service_data:
                                            entity_id: button.portail_pieton
                        b5:
                          card:
                            type: custom:button-card
                            entity: input_number.boite_aux_lettres_nombre_de_mouvement
                            show_name: false
                            show_state: false
                            icon: mdi:mailbox
                            styles:
                              icon:
                                - width: 20px
                                - color: |
                                    [[[
                                      return states['input_number.boite_aux_lettres_nombre_de_mouvement'].state == 0 ? 'rgba(0,229,255,0.35)' : '#ffc107';
                                    ]]]
                                - filter: |
                                    [[[
                                      return states['input_number.boite_aux_lettres_nombre_de_mouvement'].state == 0 ? 'none' : 'drop-shadow(0 0 5px rgba(255,193,7,0.8))';
                                    ]]]
                                - animation: |
                                    [[[
                                      return states['input_number.boite_aux_lettres_nombre_de_mouvement'].state == 0 ? 'none' : 'blink 1 steps(1) infinite';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      return states['input_number.boite_aux_lettres_nombre_de_mouvement'].state == 0 ? 'rgba(0,10,22,0.85)' : 'rgba(255,193,7,0.12)';
                                    ]]]
                                - border: |
                                    [[[
                                      return states['input_number.boite_aux_lettres_nombre_de_mouvement'].state == 0 ? '1px solid rgba(0,229,255,0.13)' : '1px solid rgba(255,193,7,0.45)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      return states['input_number.boite_aux_lettres_nombre_de_mouvement'].state == 0 ? 'none' : '2px 4px 14px rgba(255,193,7,0.18), inset 0 -3px 0 #ffc107';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      return states['input_number.boite_aux_lettres_nombre_de_mouvement'].state == 0 ? '42px' : '52px';
                                    ]]]
                            tap_action:
                              action: perform-action
                              perform_action: input_number.set_value
                              target:
                                entity_id: >-
                                  input_number.boite_aux_lettres_nombre_de_mouvement
                              data:
                                value: 0
                        b6:
                          card:
                            type: custom:button-card
                            show_name: false
                            show_state: false
                            icon: |
                              [[[
                                if (states["light.chauffage_sdb"]?.state === "on") return "mdi:heat-wave";
                                if (states["light.chauffe_eau"]?.state === "on") return "mdi:water-thermometer";
                                return "mdi:power-plug";
                              ]]]
                            styles:
                              icon:
                                - width: 20px
                                - color: |
                                    [[[
                                      if (states["light.chauffage_sdb"]?.state === "on" || states["light.chauffe_eau"]?.state === "on") return "rgba(255,23,68,0.9)";
                                      return "rgba(0,229,255,0.35)";
                                    ]]]
                                - filter: |
                                    [[[
                                      if (states["light.chauffage_sdb"]?.state === "on" || states["light.chauffe_eau"]?.state === "on") return "drop-shadow(0 0 5px rgba(255,23,68,0.8))";
                                      return "none";
                                    ]]]
                                - animation: |
                                    [[[
                                      return (states["light.chauffage_sdb"]?.state === "on" || states["light.chauffe_eau"]?.state === "on") ? "blink 2 ease infinite" : "none";
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      if (states["light.chauffage_sdb"]?.state === "on" || states["light.chauffe_eau"]?.state === "on") return "rgba(255,23,68,0.1)";
                                      return "rgba(0,10,22,0.85)";
                                    ]]]
                                - border: |
                                    [[[
                                      if (states["light.chauffage_sdb"]?.state === "on" || states["light.chauffe_eau"]?.state === "on") return "1px solid rgba(255,23,68,0.4)";
                                      return "1px solid rgba(0,229,255,0.13)";
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      if (states["light.chauffage_sdb"]?.state === "on" || states["light.chauffe_eau"]?.state === "on") return "2px 4px 14px rgba(255,23,68,0.18), inset 0 -3px 0 #ff1744";
                                      return "none";
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      return (states["light.chauffage_sdb"]?.state === "on" || states["light.chauffe_eau"]?.state === "on") ? "52px" : "42px";
                                    ]]]
                            tap_action:
                              action: call-service
                              service: light.toggle
                              service_data:
                                entity_id: light.chauffage_sdb
                              confirmation:
                                text: Activer / Désactiver le Chauffage SDB ?
                            hold_action:
                              action: call-service
                              service: light.toggle
                              service_data:
                                entity_id: light.chauffe_eau
                              confirmation:
                                text: Activer / Désactiver le Chauffe-eau ?
                  DecoLines: |
                    [[[
                      const motion = states['binary_sensor.camera_garage_cell_motion_detection']?.state === 'on';
                      const open = states['binary_sensor.do_porte_de_garage'].state === 'on';
                      const c = (motion || open) ? '#ff1744' : '#00e5ff';
                      const cOff = (motion || open) ? 'rgba(255,23,68,0.18)' : 'rgba(0,229,255,0.18)';
                      const divLine = (motion || open) ? 'rgba(255,23,68,0.15)' : 'rgba(0,229,255,0.12)';
                      const topLine = (motion || open)
                        ? 'linear-gradient(90deg,transparent,#ff1744 30%,#ff1744 70%,transparent)'
                        : 'linear-gradient(90deg,transparent,#00e5ff 30%,#00e5ff 70%,transparent)';
                      return `
                        <style>
                          @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }
                          @keyframes rotating { from{transform:rotate(0deg)} to{transform:rotate(360deg)} }
                        </style>
                        <div style="position:absolute;top:0;left:0;right:0;height:1px;background:${topLine};opacity:0.65;pointer-events:none;z-index:4;"></div>
                        <div style="position:absolute;bottom:68px;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,${divLine},transparent);pointer-events:none;z-index:4;"></div>
                        <div style="position:absolute;top:-1px;left:-1px;width:12px;height:12px;border-top:2px solid ${c};border-left:2px solid ${c};pointer-events:none;z-index:5;"></div>
                        <div style="position:absolute;top:-1px;right:-1px;width:12px;height:12px;border-top:2px solid ${c};border-right:2px solid ${c};pointer-events:none;z-index:5;"></div>
                        <div style="position:absolute;bottom:-1px;left:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-left:2px solid ${cOff};pointer-events:none;z-index:5;"></div>
                        <div style="position:absolute;bottom:-1px;right:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-right:2px solid ${cOff};pointer-events:none;z-index:5;"></div>
                      `;
                    ]]]
              - type: custom:button-card
                entity: light.chambre_parentale
                show_name: false
                show_state: false
                show_icon: false
                triggers_update:
                  - device_tracker.fire_tv
                  - binary_sensor.do_fenetre_chambre_parentale
                  - light.chauffage_chambre_parentale_fp
                  - input_boolean.ch_parent_veill
                  - input_boolean.ch_parent_norm
                  - input_boolean.ch_parents_nuit
                  - light.prise_tv_chambre
                  - light.imprimante
                tap_action:
                  action: none
                extra_styles: >
                  @keyframes border-pulse {
                    0%   { box-shadow: 0 0 10px 2px rgba(255,23,68,0.5); }
                    10%  { box-shadow: 0 0 22px 6px rgba(255,23,68,0.9); }
                    25%  { box-shadow: 0 0 14px 3px rgba(255,23,68,0.5); }
                    50%  { box-shadow: 0 0 25px 7px rgba(255,23,68,0.7); }
                    100% { box-shadow: 0 0 10px 2px rgba(255,23,68,0.5); }
                  }

                  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }

                  @keyframes bass-pulse {
                    0%   { transform:scale(1); }
                    10%  { transform:scale(1.1); }
                    50%  { transform:scale(1.08); }
                    100% { transform:scale(1); }
                  }

                  @keyframes rotating { from{transform:rotate(0deg)}
                  to{transform:rotate(360deg)} }
                styles:
                  card:
                    - height: 170px
                    - border-radius: 6px
                    - background: |
                        [[[
                          if (states['device_tracker.fire_tv']?.state === 'home') return 'rgba(255,23,68,0.04)';
                          return entity.state === 'on' ? 'rgba(255,193,7,0.03)' : 'rgba(0,229,255,0.03)';
                        ]]]
                    - border: |
                        [[[
                          if (states['device_tracker.fire_tv']?.state === 'home') return '1px solid rgba(255,23,68,0.5)';
                          return entity.state === 'on' ? '1px solid rgba(255,193,7,0.22)' : '1px solid rgba(0,229,255,0.2)';
                        ]]]
                    - box-shadow: |
                        [[[
                          if (states['device_tracker.fire_tv']?.state === 'home') return '0 0 40px rgba(255,23,68,0.2)';
                          return entity.state === 'on' ? '0 0 40px rgba(255,193,7,0.07)' : '0 0 40px rgba(0,229,255,0.06)';
                        ]]]
                    - animation: |
                        [[[
                          return states['device_tracker.fire_tv']?.state === 'home' ? 'border-pulse 0.8 ease-out infinite' : 'none';
                        ]]]
                    - padding: 0
                    - overflow: visible
                    - position: relative
                  grid:
                    - grid-template-areas: '"ico_main titre_chambre" "b_bas b_bas"'
                    - grid-template-columns: 95px 1fr
                    - grid-template-rows: 102px 68px
                  custom_fields:
                    ico_main:
                      - z-index: 2
                    titre_chambre:
                      - z-index: 2
                    b_bas:
                      - justify-self: stretch
                      - align-self: stretch
                      - z-index: 2
                    DecoLines:
                      - position: absolute
                      - top: 0
                      - left: 0
                      - width: 100%
                      - height: 100%
                      - pointer-events: none
                      - z-index: 5
                custom_fields:
                  ico_main:
                    card:
                      type: custom:button-card
                      entity: light.chambre_parentale
                      show_name: false
                      show_state: false
                      icon: |
                        [[[
                          if (states['device_tracker.fire_tv']?.state === 'home') return 'cil:tv';
                          return entity.state === 'off' ? 'hue:room-guestroom-off' : 'phu:bedroom';
                        ]]]
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - height: 102px
                          - width: 95px
                          - border-right: |
                              [[[
                                if (states['device_tracker.fire_tv']?.state === 'home') return '1px solid rgba(255,23,68,0.2)';
                                return entity.state === 'on' ? '1px solid rgba(255,193,7,0.15)' : '1px solid rgba(0,229,255,0.12)';
                              ]]]
                          - border-radius: 0
                        icon:
                          - width: 75px
                          - height: 75px
                          - color: |
                              [[[
                                if (states['device_tracker.fire_tv']?.state === 'home') return '#ff1744';
                                return entity.state === 'on' ? '#ffc107' : '#00e5ff';
                              ]]]
                          - filter: |
                              [[[
                                if (states['device_tracker.fire_tv']?.state === 'home') return 'drop-shadow(0 0 12px rgba(255,23,68,0.9))';
                                const c = entity.state === 'on' ? 'rgba(255,193,7' : 'rgba(0,229,255';
                                return `drop-shadow(0 0 12px ${c},0.9)) drop-shadow(0 0 25px ${c},0.4))`;
                              ]]]
                          - animation: |
                              [[[
                                return states['device_tracker.fire_tv']?.state === 'home' ? 'bass-pulse 0.8 ease-out infinite' : 'none';
                              ]]]
                      tap_action:
                        action: fire-dom-event
                        honeycomb_menu:
                          autoclose: true
                          size: 210
                          filter_radius: 100
                          style:
                            - background: rgba(0,5,15,0.92)
                            - border: 1px solid rgba(0,229,255,0.25)
                            - box-shadow: 0 0 40px rgba(0,229,255,0.1)
                            - border-radius: 6px
                          buttons:
                            - icon: mdi:power
                              styles:
                                icon:
                                  - color: '#ff1744'
                                card:
                                  - background: rgba(255,23,68,0.1)
                                  - border: 1px solid rgba(255,23,68,0.3)
                              tap_action:
                                action: call-service
                                service: androidtv.adb_command
                                service_data:
                                  entity_id: media_player.android_tv_192_168_1_9
                                  command: POWER
                            - icon: mdi:plus
                              styles:
                                icon:
                                  - color: '#00e5ff'
                                card:
                                  - background: rgba(0,229,255,0.08)
                                  - border: 1px solid rgba(0,229,255,0.25)
                              tap_action:
                                action: call-service
                                service: androidtv.adb_command
                                service_data:
                                  entity_id: media_player.android_tv_192_168_1_9
                                  command: CHANNEL_UP
                            - icon: mdi:volume-plus
                              styles:
                                icon:
                                  - color: '#00e5ff'
                                card:
                                  - background: rgba(0,229,255,0.08)
                                  - border: 1px solid rgba(0,229,255,0.25)
                              tap_action:
                                action: call-service
                                service: androidtv.adb_command
                                service_data:
                                  entity_id: media_player.android_tv_192_168_1_9
                                  command: VOLUME_UP
                            - icon: mdi:television
                              styles:
                                icon:
                                  - color: '#ffc107'
                                card:
                                  - background: rgba(255,193,7,0.08)
                                  - border: 1px solid rgba(255,193,7,0.25)
                              tap_action:
                                action: call-service
                                service: light.toggle
                                service_data:
                                  entity_id: light.prise_tv_switch
                            - icon: mdi:minus
                              styles:
                                icon:
                                  - color: '#00e5ff'
                                card:
                                  - background: rgba(0,229,255,0.08)
                                  - border: 1px solid rgba(0,229,255,0.25)
                              tap_action:
                                action: call-service
                                service: androidtv.adb_command
                                service_data:
                                  entity_id: media_player.android_tv_192_168_1_9
                                  command: CHANNEL_DOWN
                            - icon: mdi:volume-minus
                              styles:
                                icon:
                                  - color: '#00e5ff'
                                card:
                                  - background: rgba(0,229,255,0.08)
                                  - border: 1px solid rgba(0,229,255,0.25)
                              tap_action:
                                action: call-service
                                service: androidtv.adb_command
                                service_data:
                                  entity_id: media_player.android_tv_192_168_1_9
                                  command: VOLUME_DOWN
                  titre_chambre: |
                    [[[
                      const tv = states['device_tracker.fire_tv']?.state === 'home';
                      const color = tv ? '#ff1744' : (entity.state === 'on' ? '#ffc107' : '#00e5ff');
                      if (tv) {
                        return `<div style="
                          display:flex; align-items:center; justify-content:center;
                          height:100%; width:100%;
                        ">
                          <ha-icon icon="phu:firetv" style="
                            width:60px; height:60px; color:#ff1744;
                            filter: drop-shadow(0 0 12px rgba(255,23,68,0.9));
                            animation: bass-pulse 0.8 ease-out infinite;
                          "></ha-icon>
                        </div>`;
                      }
                      return `<div style="
                        display:flex; align-items:center; justify-content:center;
                        text-align:center; height:100%; width:100%;
                        font-family: Orbitron, sans-serif;
                        font-size: 32px;
                        font-weight: 700;
                        letter-spacing: 3px;
                        color: ${color};
                        text-shadow: 0 0 20px ${color}, 0 0 40px ${color}40;
                        white-space: nowrap;
                      ">Ch. Parentale</div>`;
                    ]]]
                  b_bas:
                    card:
                      type: custom:button-card
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - padding: 0 6px
                          - height: 68px
                          - display: flex
                          - align-items: flex-end
                          - overflow: visible
                        grid:
                          - grid-template-columns: repeat(6, 1fr)
                          - grid-template-areas: '"b1 b2 b3 b4 b5 b6"'
                          - gap: 4px
                          - align-items: flex-end
                      custom_fields:
                        b1:
                          card:
                            type: custom:button-card
                            entity: light.prise_tv_chambre
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return states['light.prise_tv_chambre'].state
                              === 'on' ? 'mdi:television' :
                              'mdi:television-off'; ]]]
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                              - value: unavailable
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: toggle
                        b2:
                          card:
                            type: custom:button-card
                            entity: input_boolean.ch_parent_norm
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return
                              states['input_boolean.ch_parent_norm'].state ===
                              'on' ? 'mdi:lightbulb' : 'mdi:lightbulb-outline';
                              ]]]
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: call-service
                              service: input_boolean.toggle
                              service_data:
                                entity_id: input_boolean.ch_parent_norm
                            hold_action:
                              action: more-info
                              entity: light.lum_chambre_parents
                        b3:
                          card:
                            type: custom:button-card
                            entity: input_boolean.ch_parent_veill
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return
                              states['input_boolean.ch_parent_veill'].state ===
                              'on' ? 'mdi:lightbulb-night' :
                              'mdi:lightbulb-night-outline'; ]]]
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: call-service
                              service: input_boolean.toggle
                              service_data:
                                entity_id: input_boolean.ch_parent_veill
                        b4:
                          card:
                            type: custom:button-card
                            entity: input_boolean.ch_parents_nuit
                            show_name: false
                            show_state: false
                            icon: mdi:weather-night
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: call-service
                              service: input_boolean.toggle
                              service_data:
                                entity_id: input_boolean.ch_parents_nuit
                        b5:
                          card:
                            type: custom:button-card
                            entity: light.imprimante
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return states['light.imprimante'].state ===
                              'on' ? 'mdi:printer' : 'mdi:printer-off'; ]]]
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                              - value: unavailable
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: toggle
                        b6:
                          card:
                            type: custom:button-card
                            entity: cover.volet_chambre_parentale
                            show_name: false
                            show_state: false
                            icon: |
                              [[[
                                if (states['binary_sensor.do_fenetre_chambre_parentale'].state === 'on') return 'mdi:window-open-variant';
                                if (states['light.chauffage_chambre_parentale_fp'].state === 'on') return 'mdi:heat-wave';
                                return states['cover.volet_chambre_parentale'].state === 'open' ? 'mdi:window-shutter-open' : 'mdi:window-shutter';
                              ]]]
                            styles:
                              icon:
                                - color: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_parentale'].state === 'on' || states['light.chauffage_chambre_parentale_fp'].state === 'on') return 'rgba(255,23,68,0.9)';
                                      return states['cover.volet_chambre_parentale'].state === 'open' ? '#ffc107' : 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_parentale'].state === 'on' || states['light.chauffage_chambre_parentale_fp'].state === 'on') return 'drop-shadow(0 0 5px rgba(255,23,68,0.8))';
                                      return states['cover.volet_chambre_parentale'].state === 'open' ? 'drop-shadow(0 0 6px rgba(255,193,7,0.8))' : 'none';
                                    ]]]
                                - animation: |
                                    [[[
                                      return (states['binary_sensor.do_fenetre_chambre_parentale'].state === 'on' || states['light.chauffage_chambre_parentale_fp'].state === 'on') ? 'blink 2 ease infinite' : 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_parentale'].state === 'on' || states['light.chauffage_chambre_parentale_fp'].state === 'on') return 'rgba(255,23,68,0.1)';
                                      return states['cover.volet_chambre_parentale'].state === 'open' ? 'rgba(255,193,7,0.12)' : 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_parentale'].state === 'on' || states['light.chauffage_chambre_parentale_fp'].state === 'on') return '1px solid rgba(255,23,68,0.45)';
                                      return states['cover.volet_chambre_parentale'].state === 'open' ? '1px solid rgba(255,193,7,0.45)' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_parentale'].state === 'on' || states['light.chauffage_chambre_parentale_fp'].state === 'on') return '2px 4px 14px rgba(255,23,68,0.18), inset 0 -3px 0 #ff1744';
                                      return states['cover.volet_chambre_parentale'].state === 'open' ? '2px 4px 14px rgba(255,193,7,0.18), inset 0 -3px 0 #ffc107' : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: 52px
                            tap_action:
                              action: call-service
                              service: cover.toggle
                              service_data:
                                entity_id: cover.volet_chambre_parentale
                  DecoLines: |
                    [[[
                      const tv = states['device_tracker.fire_tv']?.state === 'home';
                      const isOn = entity.state === 'on';
                      const c = tv ? '#ff1744' : (isOn ? '#ffc107' : '#00e5ff');
                      const cOff = tv ? 'rgba(255,23,68,0.18)' : (isOn ? 'rgba(255,193,7,0.18)' : 'rgba(0,229,255,0.18)');
                      const divLine = tv ? 'rgba(255,23,68,0.15)' : (isOn ? 'rgba(255,193,7,0.15)' : 'rgba(0,229,255,0.12)');
                      const topLine = tv
                        ? 'linear-gradient(90deg,transparent,#ff1744 30%,#ff1744 70%,transparent)'
                        : (isOn
                          ? 'linear-gradient(90deg,transparent,#ffc107 30%,#ffc107 70%,transparent)'
                          : 'linear-gradient(90deg,transparent,#00e5ff 30%,#00e5ff 70%,transparent)');
                      return `
                        <div style="position:absolute;top:0;left:0;right:0;height:1px;background:${topLine};opacity:0.65;pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;bottom:68px;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,${divLine},transparent);pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;top:-1px;left:-1px;width:12px;height:12px;border-top:2px solid ${c};border-left:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;top:-1px;right:-1px;width:12px;height:12px;border-top:2px solid ${c};border-right:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;left:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-left:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;right:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-right:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                      `;
                    ]]]
              - type: custom:button-card
                entity: light.chambres_garcons
                show_name: false
                show_state: false
                show_icon: false
                tap_action:
                  action: navigate
                  navigation_path: /lovelace/chambre_garcons
                styles:
                  card:
                    - height: 170px
                    - border-radius: 6px
                    - background: |
                        [[[
                          return entity.state === 'on' ? 'rgba(255,193,7,0.03)' : 'rgba(0,229,255,0.03)';
                        ]]]
                    - border: |
                        [[[
                          return entity.state === 'on' ? '1px solid rgba(255,193,7,0.22)' : '1px solid rgba(0,229,255,0.2)';
                        ]]]
                    - box-shadow: |
                        [[[
                          return entity.state === 'on' ? '0 0 40px rgba(255,193,7,0.07)' : '0 0 40px rgba(0,229,255,0.06)';
                        ]]]
                    - padding: 0
                    - overflow: visible
                    - position: relative
                  grid:
                    - grid-template-areas: '"ico_main titre_garcons" "b_bas b_bas"'
                    - grid-template-columns: 95px 1fr
                    - grid-template-rows: 102px 68px
                  custom_fields:
                    ico_main:
                      - z-index: 2
                    titre_garcons:
                      - z-index: 2
                    b_bas:
                      - justify-self: stretch
                      - align-self: stretch
                      - z-index: 2
                    DecoLines:
                      - position: absolute
                      - top: 0
                      - left: 0
                      - width: 100%
                      - height: 100%
                      - pointer-events: none
                      - z-index: 5
                custom_fields:
                  ico_main:
                    card:
                      type: custom:button-card
                      entity: light.chambres_garcons
                      show_name: false
                      show_state: false
                      icon: |
                        [[[
                          return entity.state === 'off' ? 'hue:room-guestroom-off' : 'phu:kids-room';
                        ]]]
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - height: 102px
                          - width: 95px
                          - border-right: |
                              [[[
                                return entity.state === 'on' ? '1px solid rgba(255,193,7,0.15)' : '1px solid rgba(0,229,255,0.12)';
                              ]]]
                          - border-radius: 0
                        icon:
                          - width: 75px
                          - height: 75px
                          - color: |
                              [[[
                                return entity.state === 'on' ? '#ffc107' : '#00e5ff';
                              ]]]
                          - filter: |
                              [[[
                                const c = entity.state === 'on' ? 'rgba(255,193,7' : 'rgba(0,229,255';
                                return `drop-shadow(0 0 12px ${c},0.9)) drop-shadow(0 0 25px ${c},0.4))`;
                              ]]]
                  titre_garcons: |
                    [[[
                      const color = entity.state === 'on' ? '#ffc107' : '#00e5ff';
                      return `<div style="
                        display: flex;
                        align-items: center;
                        justify-content: center;
                        text-align: center;
                        height: 100%;
                        width: 100%;
                        font-family: Orbitron, sans-serif;
                        font-size: 32px;
                        font-weight: 700;
                        letter-spacing: 3px;
                        color: ${color};
                        text-shadow: 0 0 20px ${color}, 0 0 40px ${color}40;
                        white-space: nowrap;
                      ">Ch. Garçons</div>`;
                    ]]]
                  b_bas:
                    card:
                      type: custom:button-card
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - padding: 0 6px
                          - height: 68px
                          - display: flex
                          - align-items: flex-end
                          - overflow: visible
                        grid:
                          - grid-template-columns: repeat(6, 1fr)
                          - grid-template-areas: '"b1 b2 b3 b4 b5 b6"'
                          - gap: 4px
                          - align-items: flex-end
                      custom_fields:
                        b1:
                          card:
                            type: custom:button-card
                            entity: cover.volet_chris
                            show_name: false
                            show_state: false
                            icon: |
                              [[[
                                if (states['binary_sensor.do_fenetre_chambre_chris'].state === 'on') return 'mdi:window-open-variant';
                                if (states['light.chauffage_chambre_chris_fp'].state === 'on') return 'mdi:heat-wave';
                                return states['cover.volet_chris'].state === 'open' ? 'mdi:window-shutter-open' : 'mdi:window-shutter';
                              ]]]
                            styles:
                              icon:
                                - color: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_chris'].state === 'on' || states['light.chauffage_chambre_chris_fp'].state === 'on') return 'rgba(255,23,68,0.9)';
                                      return states['cover.volet_chris'].state === 'open' ? '#ffc107' : 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_chris'].state === 'on' || states['light.chauffage_chambre_chris_fp'].state === 'on') return 'drop-shadow(0 0 5px rgba(255,23,68,0.8))';
                                      return states['cover.volet_chris'].state === 'open' ? 'drop-shadow(0 0 6px rgba(255,193,7,0.8))' : 'none';
                                    ]]]
                                - animation: |
                                    [[[
                                      return (states['binary_sensor.do_fenetre_chambre_chris'].state === 'on' || states['light.chauffage_chambre_chris_fp'].state === 'on') ? 'blink 2 ease infinite' : 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_chris'].state === 'on' || states['light.chauffage_chambre_chris_fp'].state === 'on') return 'rgba(255,23,68,0.12)';
                                      return states['cover.volet_chris'].state === 'open' ? 'rgba(255,193,7,0.12)' : 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_chris'].state === 'on' || states['light.chauffage_chambre_chris_fp'].state === 'on') return '1px solid rgba(255,23,68,0.45)';
                                      return states['cover.volet_chris'].state === 'open' ? '1px solid rgba(255,193,7,0.45)' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_chris'].state === 'on' || states['light.chauffage_chambre_chris_fp'].state === 'on') return '2px 4px 14px rgba(255,23,68,0.18), inset 0 -3px 0 #ff1744';
                                      return states['cover.volet_chris'].state === 'open' ? '2px 4px 14px rgba(255,193,7,0.18), inset 0 -3px 0 #ffc107' : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      return (states['binary_sensor.do_fenetre_chambre_chris'].state === 'on' || states['light.chauffage_chambre_chris_fp'].state === 'on' || states['cover.volet_chris'].state === 'open') ? '52px' : '42px';
                                    ]]]
                            tap_action:
                              action: call-service
                              service: cover.toggle
                              service_data:
                                entity_id: cover.volet_chris
                        b2:
                          card:
                            type: custom:button-card
                            entity: light.interrupteur_chambre_chris
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return
                              states['light.interrupteur_chambre_chris'].state
                              === 'on' ? 'mdi:lightbulb' :
                              'mdi:lightbulb-outline'; ]]]
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                              - value: unavailable
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: toggle
                            hold_action:
                              action: more-info
                              entity: light.lumiere_chambre_chris
                        b3:
                          card:
                            type: custom:button-card
                            entity: light.veilleuse
                            show_name: false
                            show_state: false
                            icon: |
                              [[[
                                if (states['light.veilleuse'].state === 'on') return 'mdi:lightbulb-night';
                                if (states['device_tracker.tablette_ha'].state === 'home') return 'mdi:tablet';
                                return 'mdi:lightbulb-night-outline';
                              ]]]
                            styles:
                              icon:
                                - color: |
                                    [[[
                                      if (states['light.veilleuse'].state === 'on') return '#ffc107';
                                      if (states['device_tracker.tablette_ha'].state === 'home') return 'limegreen';
                                      return 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      if (states['light.veilleuse'].state === 'on') return 'drop-shadow(0 0 6px rgba(255,193,7,0.8))';
                                      if (states['device_tracker.tablette_ha'].state === 'home') return 'drop-shadow(0 0 6px rgba(50,205,50,0.8))';
                                      return 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      if (states['light.veilleuse'].state === 'on') return 'rgba(255,193,7,0.12)';
                                      if (states['device_tracker.tablette_ha'].state === 'home') return 'rgba(50,205,50,0.15)';
                                      return 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      if (states['light.veilleuse'].state === 'on') return '1px solid rgba(255,193,7,0.45)';
                                      if (states['device_tracker.tablette_ha'].state === 'home') return '1px solid rgba(50,205,50,0.45)';
                                      return '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      if (states['light.veilleuse'].state === 'on') return '2px 4px 14px rgba(255,193,7,0.18), inset 0 -3px 0 #ffc107';
                                      if (states['device_tracker.tablette_ha'].state === 'home') return '2px 4px 14px rgba(50,205,50,0.18), inset 0 -3px 0 limegreen';
                                      return 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      return (states['light.veilleuse'].state === 'on' || states['device_tracker.tablette_ha'].state === 'home') ? '52px' : '42px';
                                    ]]]
                            tap_action:
                              action: toggle
                        b4:
                          card:
                            type: custom:button-card
                            entity: sensor.pronote_moyenne
                            show_name: false
                            show_state: false
                            icon: |
                              [[[
                                return states['device_tracker.pctimeo'].state === 'home' ? 'mdi:desktop-tower-monitor' : 'mdi:school';
                              ]]]
                            styles:
                              icon:
                                - color: limegreen
                                - filter: drop-shadow(0 0 5px rgba(50,205,50,0.7))
                              card:
                                - background: |
                                    [[[
                                      return states['device_tracker.pctimeo'].state === 'home' ? 'rgba(50,205,50,0.15)' : 'rgba(50,205,50,0.08)';
                                    ]]]
                                - border: |
                                    [[[
                                      return states['device_tracker.pctimeo'].state === 'home' ? '1px solid rgba(50,205,50,0.45)' : '1px solid rgba(50,205,50,0.25)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      return states['device_tracker.pctimeo'].state === 'home' ? '2px 4px 14px rgba(50,205,50,0.18), inset 0 -3px 0 limegreen' : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      return states['device_tracker.pctimeo'].state === 'home' ? '52px' : '42px';
                                    ]]]
                            tap_action:
                              action: navigate
                              navigation_path: /dashboard-timeo/notes
                        b5:
                          card:
                            type: custom:button-card
                            entity: light.interrupteur_chambre_timeo
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return
                              states['light.interrupteur_chambre_timeo'].state
                              === 'on' ? 'mdi:lightbulb' :
                              'mdi:lightbulb-outline'; ]]]
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                              - value: unavailable
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: toggle
                            hold_action:
                              action: more-info
                              entity: light.lumiere_chambre_timeo_lumiere
                        b6:
                          card:
                            type: custom:button-card
                            entity: cover.volet_timeo
                            show_name: false
                            show_state: false
                            icon: |
                              [[[
                                if (states['binary_sensor.do_fenetre_chambre_timeo'].state === 'on') return 'mdi:window-open-variant';
                                if (states['switch.chauffage_chambre_timeo_fp'].state === 'on') return 'mdi:heat-wave';
                                return states['cover.volet_timeo'].state === 'open' ? 'mdi:window-shutter-open' : 'mdi:window-shutter';
                              ]]]
                            styles:
                              icon:
                                - color: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_timeo'].state === 'on' || states['switch.chauffage_chambre_timeo_fp'].state === 'on') return 'rgba(255,23,68,0.9)';
                                      return states['cover.volet_timeo'].state === 'open' ? '#ffc107' : 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_timeo'].state === 'on' || states['switch.chauffage_chambre_timeo_fp'].state === 'on') return 'drop-shadow(0 0 5px rgba(255,23,68,0.8))';
                                      return states['cover.volet_timeo'].state === 'open' ? 'drop-shadow(0 0 6px rgba(255,193,7,0.8))' : 'none';
                                    ]]]
                                - animation: |
                                    [[[
                                      return (states['binary_sensor.do_fenetre_chambre_timeo'].state === 'on' || states['switch.chauffage_chambre_timeo_fp'].state === 'on') ? 'blink 2 ease infinite' : 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_timeo'].state === 'on' || states['switch.chauffage_chambre_timeo_fp'].state === 'on') return 'rgba(255,23,68,0.12)';
                                      return states['cover.volet_timeo'].state === 'open' ? 'rgba(255,193,7,0.12)' : 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_timeo'].state === 'on' || states['switch.chauffage_chambre_timeo_fp'].state === 'on') return '1px solid rgba(255,23,68,0.45)';
                                      return states['cover.volet_timeo'].state === 'open' ? '1px solid rgba(255,193,7,0.45)' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      if (states['binary_sensor.do_fenetre_chambre_timeo'].state === 'on' || states['switch.chauffage_chambre_timeo_fp'].state === 'on') return '2px 4px 14px rgba(255,23,68,0.18), inset 0 -3px 0 #ff1744';
                                      return states['cover.volet_timeo'].state === 'open' ? '2px 4px 14px rgba(255,193,7,0.18), inset 0 -3px 0 #ffc107' : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      return (states['binary_sensor.do_fenetre_chambre_timeo'].state === 'on' || states['switch.chauffage_chambre_timeo_fp'].state === 'on' || states['cover.volet_timeo'].state === 'open') ? '52px' : '42px';
                                    ]]]
                            tap_action:
                              action: call-service
                              service: cover.toggle
                              service_data:
                                entity_id: cover.volet_timeo
                  DecoLines: |
                    [[[
                      const isOn = entity.state === 'on';
                      const c = isOn ? '#ffc107' : '#00e5ff';
                      const cOff = isOn ? 'rgba(255,193,7,0.18)' : 'rgba(0,229,255,0.18)';
                      const divLine = isOn ? 'rgba(255,193,7,0.15)' : 'rgba(0,229,255,0.12)';
                      const topLine = isOn
                        ? 'linear-gradient(90deg,transparent,#ffc107 30%,#ffc107 70%,transparent)'
                        : 'linear-gradient(90deg,transparent,#00e5ff 30%,#00e5ff 70%,transparent)';
                      return `
                        <style>
                          @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }
                        </style>
                        <div style="position:absolute;top:0;left:0;right:0;height:1px;background:${topLine};opacity:0.65;pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;bottom:68px;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,${divLine},transparent);pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;top:-1px;left:-1px;width:12px;height:12px;border-top:2px solid ${c};border-left:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;top:-1px;right:-1px;width:12px;height:12px;border-top:2px solid ${c};border-right:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;left:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-left:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;right:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-right:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                      `;
                    ]]]
              - type: custom:button-card
                entity: sensor.etat_de_l_alame
                show_name: false
                show_state: false
                show_icon: false
                tap_action:
                  action: navigate
                  navigation_path: /lovelace/alarme
                styles:
                  card:
                    - height: 170px
                    - border-radius: 6px
                    - background: |
                        [[[
                          const cams = [
                            'image.bc1c_ba3981580_derniere_image_du_mouvement',
                            'image.cote_rue_derniere_image_du_mouvement',
                            'image.parking_derniere_image_du_mouvement',
                            'image.terrasse_derniere_image_du_mouvement'
                          ];
                          const latest = cams
                            .map(id => states[id])
                            .filter(s => s && s.state !== 'unknown')
                            .sort((a, b) => new Date(b.last_updated) - new Date(a.last_updated))[0];
                          const s = states['sensor.etat_de_l_alame'].state;
                          const isArmed = s === "('armed',)";
                          const isPartial = s === "('unknown',)";
                          const overlay = isArmed ? 'rgba(255,23,68,0.15)' : isPartial ? 'rgba(255,193,7,0.1)' : 'rgba(0,10,22,0.6)';
                          if (latest) return `linear-gradient(${overlay}, ${overlay}), url("${latest.attributes.entity_picture}")`;
                          return isArmed ? 'rgba(255,23,68,0.03)' : isPartial ? 'rgba(255,193,7,0.03)' : 'rgba(0,229,255,0.03)';
                        ]]]
                    - background-size: cover
                    - background-position: center
                    - background-repeat: no-repeat
                    - border: |
                        [[[
                          const s = states['sensor.etat_de_l_alame'].state;
                          if (s === "('armed',)") return '1px solid rgba(255,23,68,0.55)';
                          if (s === "('unknown',)") return '1px solid rgba(255,193,7,0.4)';
                          return '1px solid rgba(0,229,255,0.2)';
                        ]]]
                    - box-shadow: |
                        [[[
                          const s = states['sensor.etat_de_l_alame'].state;
                          if (s === "('armed',)") return '0 0 40px rgba(255,23,68,0.2)';
                          if (s === "('unknown',)") return '0 0 40px rgba(255,193,7,0.1)';
                          return '0 0 40px rgba(0,229,255,0.06)';
                        ]]]
                    - padding: 0
                    - overflow: visible
                    - position: relative
                  grid:
                    - grid-template-areas: '"ico_main titre_secu" "b_bas b_bas"'
                    - grid-template-columns: 95px 1fr
                    - grid-template-rows: 102px 68px
                  custom_fields:
                    ico_main:
                      - z-index: 2
                    titre_secu:
                      - z-index: 2
                    b_bas:
                      - justify-self: stretch
                      - align-self: stretch
                      - z-index: 2
                    DecoLines:
                      - position: absolute
                      - top: 0
                      - left: 0
                      - width: 100%
                      - height: 100%
                      - pointer-events: none
                      - z-index: 5
                custom_fields:
                  ico_main:
                    card:
                      type: custom:button-card
                      entity: sensor.etat_de_l_alame
                      show_name: false
                      show_state: false
                      icon: |
                        [[[
                          const s = states['sensor.etat_de_l_alame'].state;
                          if (s === "('armed',)") return 'mdi:shield-home';
                          if (s === "('unknown',)") return 'mdi:shield-half-full';
                          return 'mdi:shield-off';
                        ]]]
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - height: 102px
                          - width: 95px
                          - border-right: |
                              [[[
                                const s = states['sensor.etat_de_l_alame'].state;
                                if (s === "('armed',)") return '1px solid rgba(255,23,68,0.2)';
                                if (s === "('unknown',)") return '1px solid rgba(255,193,7,0.15)';
                                return '1px solid rgba(0,229,255,0.12)';
                              ]]]
                          - border-radius: 0
                        icon:
                          - width: 75px
                          - height: 75px
                          - color: |
                              [[[
                                const s = states['sensor.etat_de_l_alame'].state;
                                if (s === "('armed',)") return '#ff1744';
                                if (s === "('unknown',)") return '#ffc107';
                                return 'limegreen';
                              ]]]
                          - filter: |
                              [[[
                                const s = states['sensor.etat_de_l_alame'].state;
                                if (s === "('armed',)") return 'drop-shadow(0 0 12px rgba(255,23,68,0.9)) drop-shadow(0 0 25px rgba(255,23,68,0.4))';
                                if (s === "('unknown',)") return 'drop-shadow(0 0 12px rgba(255,193,7,0.9)) drop-shadow(0 0 25px rgba(255,193,7,0.4))';
                                return 'drop-shadow(0 0 12px rgba(50,205,50,0.9)) drop-shadow(0 0 25px rgba(50,205,50,0.4))';
                              ]]]
                  titre_secu: |
                    [[[
                      const s = states['sensor.etat_de_l_alame'].state;
                      const isArmed = s === "('armed',)";
                      const isPartial = s === "('unknown',)";
                      const color = isArmed ? '#ff1744' : isPartial ? '#ffc107' : 'limegreen';
                      const halo = isArmed ? '255,23,68' : isPartial ? '255,193,7' : '50,205,50';
                      const label = isArmed ? 'Total' : isPartial ? 'Partiel' : 'Désarmé';
                      return `<div style="
                        display: flex;
                        align-items: center;
                        justify-content: center;
                        text-align: center;
                        height: 100%;
                        width: 100%;
                        font-family: Orbitron, sans-serif;
                        font-size: 32px;
                        font-weight: 700;
                        letter-spacing: 4px;
                        color: ${color};
                        text-shadow: 0 0 12px rgba(${halo},0.9), 0 0 25px rgba(${halo},0.4);
                        white-space: nowrap;
                        padding: 0 8px;
                      ">${label}</div>`;
                    ]]]
                  b_bas:
                    card:
                      type: custom:button-card
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - padding: 0 6px
                          - height: 68px
                          - display: flex
                          - align-items: flex-end
                          - overflow: visible
                        grid:
                          - grid-template-columns: repeat(6, 1fr)
                          - grid-template-areas: '"b1 b2 b3 b4 b5 b6"'
                          - gap: 4px
                          - align-items: flex-end
                      custom_fields:
                        b1:
                          card:
                            type: custom:button-card
                            icon: mdi:shield-off
                            show_name: false
                            show_state: false
                            styles:
                              icon:
                                - color: |
                                    [[[
                                      const s = states['sensor.etat_de_l_alame'].state;
                                      return (s === "('disarmed',)" || s === 'unknown') ? 'limegreen' : 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      const s = states['sensor.etat_de_l_alame'].state;
                                      return (s === "('disarmed',)" || s === 'unknown') ? 'drop-shadow(0 0 6px rgba(50,205,50,0.8))' : 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      const s = states['sensor.etat_de_l_alame'].state;
                                      return (s === "('disarmed',)" || s === 'unknown') ? 'rgba(50,205,50,0.1)' : 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      const s = states['sensor.etat_de_l_alame'].state;
                                      return (s === "('disarmed',)" || s === 'unknown') ? '1px solid rgba(50,205,50,0.4)' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      const s = states['sensor.etat_de_l_alame'].state;
                                      return (s === "('disarmed',)" || s === 'unknown') ? '2px 4px 14px rgba(50,205,50,0.15), inset 0 -3px 0 limegreen' : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      const s = states['sensor.etat_de_l_alame'].state;
                                      return (s === "('disarmed',)" || s === 'unknown') ? '52px' : '42px';
                                    ]]]
                            tap_action:
                              action: call-service
                              service: alarm_control_panel.alarm_disarm
                              service_data:
                                entity_id: alarm_control_panel.alarme_risco_virtuelle
                        b2:
                          card:
                            type: custom:button-card
                            icon: mdi:shield-half-full
                            show_name: false
                            show_state: false
                            styles:
                              icon:
                                - color: |
                                    [[[
                                      return states['sensor.etat_de_l_alame'].state === "('unknown',)" ? '#ffc107' : 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      return states['sensor.etat_de_l_alame'].state === "('unknown',)" ? 'drop-shadow(0 0 6px rgba(255,193,7,0.8))' : 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      return states['sensor.etat_de_l_alame'].state === "('unknown',)" ? 'rgba(255,193,7,0.12)' : 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      return states['sensor.etat_de_l_alame'].state === "('unknown',)" ? '1px solid rgba(255,193,7,0.45)' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      return states['sensor.etat_de_l_alame'].state === "('unknown',)" ? '2px 4px 14px rgba(255,193,7,0.18), inset 0 -3px 0 #ffc107' : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      return states['sensor.etat_de_l_alame'].state === "('unknown',)" ? '52px' : '42px';
                                    ]]]
                        b3:
                          card:
                            type: custom:button-card
                            icon: mdi:shield-home
                            show_name: false
                            show_state: false
                            styles:
                              icon:
                                - color: |
                                    [[[
                                      return states['sensor.etat_de_l_alame'].state === "('armed',)" ? '#ff1744' : 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      return states['sensor.etat_de_l_alame'].state === "('armed',)" ? 'drop-shadow(0 0 6px rgba(255,23,68,0.8))' : 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      return states['sensor.etat_de_l_alame'].state === "('armed',)" ? 'rgba(255,23,68,0.1)' : 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      return states['sensor.etat_de_l_alame'].state === "('armed',)" ? '1px solid rgba(255,23,68,0.4)' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      return states['sensor.etat_de_l_alame'].state === "('armed',)" ? '2px 4px 14px rgba(255,23,68,0.18), inset 0 -3px 0 #ff1744' : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      return states['sensor.etat_de_l_alame'].state === "('armed',)" ? '52px' : '42px';
                                    ]]]
                            tap_action:
                              action: call-service
                              service: alarm_control_panel.alarm_arm_away
                              service_data:
                                entity_id: alarm_control_panel.alarme_risco_virtuelle
                        b4:
                          card:
                            type: custom:button-card
                            entity: binary_sensor.ouvertures
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: |
                                    [[[
                                      const st = states['binary_sensor.ouvertures']?.state;
                                      const isOpen = st === 'on';
                                      return isOpen ? 'rgba(255,23,68,0.12)' : 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      const st = states['binary_sensor.ouvertures']?.state;
                                      const isOpen = st === 'on';
                                      return isOpen ? '1px solid rgba(255,23,68,0.45)' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      const st = states['binary_sensor.ouvertures']?.state;
                                      const isOpen = st === 'on';
                                      return isOpen
                                        ? '2px 4px 14px rgba(255,23,68,0.18), inset 0 -3px 0 #ff1744'
                                        : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      const st = states['binary_sensor.ouvertures']?.state;
                                      const isOpen = st === 'on';
                                      return isOpen ? '52px' : '42px';
                                    ]]]
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  const entities = [
                                    'binary_sensor.do_porte_de_garage',
                                    'binary_sensor.do_po_d_entree',
                                    'binary_sensor.do_pf_salle_a_manger_droite',
                                    'binary_sensor.do_pf_terrasse',
                                    'binary_sensor.do_pf_cuisine',
                                    'binary_sensor.do_fenetre_chambre_parentale',
                                    'binary_sensor.do_fenetre_chambre_timeo',
                                    'binary_sensor.do_fenetre_chambre_chris'
                                   ];
                                  const openCount = entities.filter(e => states[e]?.state === 'on').length;
                                  const isOpen = openCount > 0;
                                  const color = isOpen ? '#ff1744' : 'rgba(0,229,255,0.35)';
                                  const glow = isOpen ? `filter:drop-shadow(0 0 6px ${color});` : '';
                                  const anim = isOpen ? 'animation: blink 2 ease infinite;' : '';

                                  return `
                                    <div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;gap:4px;${anim}">
                                      <ha-icon icon="${isOpen ? 'mdi:window-open-variant' : 'mdi:window-closed-variant'}"
                                        style="width:22px;height:22px;color:${color};${glow}">
                                      </ha-icon>
                                      ${isOpen ? `
                                        <span style="
                                          font-family:Orbitron,sans-serif;
                                          font-size:14px;
                                          font-weight:700;
                                          color:${color};
                                          text-shadow:0 0 8px ${color};
                                        ">${openCount}</span>
                                      ` : ''}
                                    </div>
                                  `;
                                ]]]
                            tap_action:
                              action: fire-dom-event
                              browser_mod:
                                service: browser_mod.popup
                                data:
                                  title: OUVERTURES
                                  style: >
                                    --popup-background: rgba(0, 5, 15, 0.96);
                                    --popup-border-radius: 6px;
                                    --popup-border-color: rgba(0,229,255,0.22);
                                    --popup-box-shadow: 0 0 40px
                                    rgba(0,229,255,0.08); --ha-card-background:
                                    transparent; --mdc-theme-surface: rgba(0, 5,
                                    15, 0.96); --primary-text-color: #00e5ff;
                                    --secondary-text-color:
                                    rgba(0,229,255,0.55); --mdc-theme-primary:
                                    #00e5ff; backdrop-filter: blur(12px);
                                  content:
                                    type: entities
                                    entities:
                                      - entity: binary_sensor.do_porte_de_garage
                                        name: Porte de Garage
                                      - entity: binary_sensor.do_po_d_entree
                                        name: Porte d'Entrée
                                      - entity: >-
                                          binary_sensor.do_pf_salle_a_manger_droite
                                        name: PF SàM Droite
                                      - entity: binary_sensor.do_pf_terrasse
                                        name: PF SàM Terrasse
                                      - entity: binary_sensor.do_pf_cuisine
                                        name: PF Cuisine
                                      - entity: >-
                                          binary_sensor.do_fenetre_chambre_parentale
                                        name: F. Ch. Parentale
                                      - entity: binary_sensor.do_fenetre_chambre_timeo
                                        name: F. Ch. Timéo
                                      - entity: binary_sensor.do_fenetre_chambre_chris
                                        name: F. Ch. Chris
                                    state_color: true
                                    show_header_toggle: false
                        b5:
                          card:
                            type: custom:button-card
                            entity: cover.volets
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return states['cover.volets'].state ===
                              'closed' ? 'mdi:window-shutter' :
                              'mdi:window-shutter-open'; ]]]
                            styles:
                              icon:
                                - color: |
                                    [[[
                                      return states['cover.volets'].state === 'open' ? '#ffc107' : 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      return states['cover.volets'].state === 'open' ? 'drop-shadow(0 0 6px rgba(255,193,7,0.8))' : 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      return states['cover.volets'].state === 'open' ? 'rgba(255,193,7,0.12)' : 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      return states['cover.volets'].state === 'open' ? '1px solid rgba(255,193,7,0.45)' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      return states['cover.volets'].state === 'open' ? '2px 4px 14px rgba(255,193,7,0.18), inset 0 -3px 0 #ffc107' : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      return states['cover.volets'].state === 'open' ? '52px' : '42px';
                                    ]]]
                            tap_action:
                              action: call-service
                              service: |
                                [[[
                                  return states['cover.volets'].state === 'closed' ? 'cover.open_cover' : 'cover.close_cover';
                                ]]]
                              service_data:
                                entity_id: cover.volets
                              confirmation:
                                text: |
                                  [[[
                                    return states['cover.volets'].state === 'closed' ? 'Ouvrir tous les volets ?' : 'Fermer tous les volets ?';
                                  ]]]
                            hold_action:
                              action: navigate
                              navigation_path: /lovelace/lumiere
                        b6:
                          card:
                            type: custom:button-card
                            entity: alarm_control_panel.cameras
                            show_name: false
                            show_state: false
                            icon: >
                              [[[ return
                              states['alarm_control_panel.cameras'].state ===
                              'armed_away' ? 'mdi:cctv' : 'mdi:cctv-off'; ]]]
                            styles:
                              icon:
                                - color: |
                                    [[[
                                      return states['alarm_control_panel.cameras'].state === 'armed_away' ? '#ffc107' : 'rgba(0,229,255,0.35)';
                                    ]]]
                                - filter: |
                                    [[[
                                      return states['alarm_control_panel.cameras'].state === 'armed_away' ? 'drop-shadow(0 0 6px rgba(255,193,7,0.8))' : 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      return states['alarm_control_panel.cameras'].state === 'armed_away' ? 'rgba(255,193,7,0.12)' : 'rgba(0,10,22,0.85)';
                                    ]]]
                                - border: |
                                    [[[
                                      return states['alarm_control_panel.cameras'].state === 'armed_away' ? '1px solid rgba(255,193,7,0.45)' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      return states['alarm_control_panel.cameras'].state === 'armed_away' ? '2px 4px 14px rgba(255,193,7,0.18), inset 0 -3px 0 #ffc107' : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      return states['alarm_control_panel.cameras'].state === 'armed_away' ? '52px' : '42px';
                                    ]]]
                            tap_action:
                              action: call-service
                              service: alarm_control_panel.alarm_arm_away
                              target:
                                entity_id: alarm_control_panel.cameras
                  DecoLines: |
                    [[[
                      const s = states['sensor.etat_de_l_alame'].state;
                      const isArmed = s === "('armed',)";
                      const isPartial = s === "('unknown',)";
                      const c = isArmed ? '#ff1744' : isPartial ? '#ffc107' : 'limegreen';
                      const cOff = isArmed ? 'rgba(255,23,68,0.18)' : isPartial ? 'rgba(255,193,7,0.18)' : 'rgba(50,205,50,0.18)';
                      const divLine = isArmed ? 'rgba(255,23,68,0.15)' : isPartial ? 'rgba(255,193,7,0.15)' : 'rgba(50,205,50,0.12)';
                      const topLine = isArmed
                        ? 'linear-gradient(90deg,transparent,#ff1744 30%,#ff1744 70%,transparent)'
                        : isPartial
                        ? 'linear-gradient(90deg,transparent,#ffc107 30%,#ffc107 70%,transparent)'
                        : 'linear-gradient(90deg,transparent,limegreen 30%,limegreen 70%,transparent)';
                      return `
                        <style>
                          @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }
                        </style>
                        <div style="position:absolute;top:0;left:0;right:0;height:1px;background:${topLine};opacity:0.65;pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;bottom:68px;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,${divLine},transparent);pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;top:-1px;left:-1px;width:12px;height:12px;border-top:2px solid ${c};border-left:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;top:-1px;right:-1px;width:12px;height:12px;border-top:2px solid ${c};border-right:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;left:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-left:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;right:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-right:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                      `;
                    ]]]
            columns: 1
            grid_options:
              columns: full
        column_span: 1
      - type: grid
        cards:
          - square: false
            type: grid
            cards:
              - type: custom:button-card
                entity: weather.vandeins
                triggers_update:
                  - sensor.meteo_infos_detail
                  - sun.sun
                show_name: false
                show_state: false
                show_icon: false
                variables:
                  is_night: |
                    [[[ return states['sun.sun'].state === 'below_horizon'; ]]]
                tap_action:
                  action: navigate
                  navigation_path: /lovelace/meteo
                extra_styles: >
                  @keyframes rotating { from{transform:rotate(0deg)}
                  to{transform:rotate(360deg)} }

                  @keyframes pulse { 0%,100%{transform:scale(1)}
                  50%{transform:scale(1.1)} }

                  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }

                  @keyframes drift { 0%,100%{transform:translateX(0px)}
                  50%{transform:translateX(5px)} }
                styles:
                  card:
                    - height: 170px
                    - border-radius: 6px
                    - background: |
                        [[[
                          if (variables.is_night) return 'rgba(126,87,194,0.04)';
                          const s = entity.state;
                          if (s === 'sunny' || s === 'partlycloudy') return 'rgba(255,235,59,0.03)';
                          if (s === 'rainy' || s === 'lightning-rainy' || s === 'pouring') return 'rgba(68,138,255,0.03)';
                          return 'rgba(0,229,255,0.03)';
                        ]]]
                    - border: |
                        [[[
                          if (variables.is_night) return '1px solid rgba(126,87,194,0.35)';
                          const s = entity.state;
                          if (s === 'sunny' || s === 'partlycloudy') return '1px solid rgba(255,235,59,0.35)';
                          if (s === 'rainy' || s === 'lightning-rainy' || s === 'pouring') return '1px solid rgba(68,138,255,0.35)';
                          if (s === 'cloudy' || s === 'fog') return '1px solid rgba(189,189,189,0.3)';
                          return '1px solid rgba(0,229,255,0.2)';
                        ]]]
                    - box-shadow: |
                        [[[
                          if (variables.is_night) return '0 0 40px rgba(126,87,194,0.12)';
                          const s = entity.state;
                          if (s === 'sunny' || s === 'partlycloudy') return '0 0 40px rgba(255,235,59,0.1)';
                          if (s === 'rainy' || s === 'lightning-rainy' || s === 'pouring') return '0 0 40px rgba(68,138,255,0.1)';
                          return '0 0 40px rgba(0,229,255,0.06)';
                        ]]]
                    - padding: 0
                    - overflow: visible
                    - position: relative
                  grid:
                    - grid-template-areas: '"ico_main titre_meteo" "b_bas b_bas"'
                    - grid-template-columns: 95px 1fr
                    - grid-template-rows: 102px 68px
                  custom_fields:
                    ico_main:
                      - z-index: 2
                      - align-self: center
                      - justify-self: center
                    titre_meteo:
                      - z-index: 2
                      - position: relative
                      - overflow: hidden
                    cam_live:
                      - position: absolute
                      - top: 0
                      - right: 0
                      - width: 100%
                      - height: 102px
                      - z-index: 1
                      - pointer-events: none
                    b_bas:
                      - justify-self: stretch
                      - align-self: stretch
                      - z-index: 2
                    DecoLines:
                      - position: absolute
                      - top: 0
                      - left: 0
                      - width: 100%
                      - height: 100%
                      - pointer-events: none
                      - z-index: 5
                custom_fields:
                  ico_main: |
                    [[[
                      const s = entity.state;
                      const night = variables.is_night;
                      let icon = 'mdi:weather-cloudy';
                      let color = '#00e5ff';
                      let anim = 'none';

                      if (s === 'clear-night' || (s === 'sunny' && night)) {
                        icon = 'mdi:weather-night'; color = '#9575cd'; anim = 'pulse 3 ease-in-out infinite';
                      } else if (s === 'sunny') {
                        icon = 'mdi:white-balance-sunny'; color = 'yellow'; anim = 'rotating 10 linear infinite';
                      } else if (s === 'partlycloudy') {
                        icon = night ? 'mdi:weather-night-partly-cloudy' : 'mdi:weather-partly-cloudy';
                        color = night ? '#7e57c2' : '#ffeb3b'; anim = 'drift 5 ease-in-out infinite';
                      } else if (s === 'cloudy') {
                        icon = 'mdi:cloud'; color = '#bdbdbd'; anim = 'drift 5 ease-in-out infinite';
                      } else if (s === 'rainy') {
                        icon = 'mdi:weather-rainy'; color = '#448aff'; anim = 'pulse 2.5 infinite';
                      } else if (s === 'lightning-rainy') {
                        icon = 'mdi:weather-lightning-rainy'; color = '#ffeb3b'; anim = 'blink 1 infinite';
                      } else if (s === 'pouring') {
                        icon = 'mdi:weather-pouring'; color = '#448aff'; anim = 'pulse 2 infinite';
                      } else if (s === 'fog') {
                        icon = 'mdi:weather-fog'; color = '#bdbdbd'; anim = 'drift 6 ease-in-out infinite';
                      } else if (s === 'snowy') {
                        icon = 'mdi:snowflake'; color = 'white'; anim = 'pulse 3 infinite';
                      }

                      return `<div style="
                        display:flex; align-items:center; justify-content:center;
                        width:95px; height:102px;
                        border-right: 1px solid ${color}22;
                      ">
                        <ha-icon icon="${icon}" style="
                          width:70px; height:70px; color:${color};
                          filter: drop-shadow(0 0 12px ${color}) drop-shadow(0 0 25px ${color}66);
                          animation: ${anim};
                        "></ha-icon>
                      </div>`;
                    ]]]
                  titre_meteo: |
                    [[[
                      const s = entity.state;
                      const night = variables.is_night;
                      let color = '#00e5ff';
                      if (night || s === 'clear-night') color = '#9575cd';
                      else if (s === 'sunny' || s === 'partlycloudy') color = '#ffeb3b';
                      else if (s === 'rainy' || s === 'lightning-rainy' || s === 'pouring') color = '#448aff';
                      else if (s === 'cloudy' || s === 'fog') color = '#bdbdbd';

                      const labels = {
                        'sunny': 'Ensoleillé', 'clear-night': 'Nuit claire',
                        'partlycloudy': 'Peu nuageux', 'cloudy': 'Nuageux',
                        'rainy': 'Pluvieux', 'lightning-rainy': 'Orageux',
                        'pouring': 'Forte pluie', 'fog': 'Brouillard', 'snowy': 'Neige'
                      };
                      const label = labels[s] || s;

                      return `<div style="
                        display:flex; align-items:flex-end; justify-content:center;
                        text-align:center; height:100%; width:100%;
                        padding-bottom:10px; position:relative; z-index:2;
                        font-family:Orbitron, sans-serif; font-size:32px; font-weight:700;
                        letter-spacing:3px; color:${color};
                        text-shadow: 0 0 20px ${color}, 0 0 40px ${color}40;
                        white-space:nowrap;
                      ">${label}</div>`;
                    ]]]
                  cam_live: |
                    [[[
                      return `<div style="
                        position:absolute; top:0; left:0; right:0; height:102px;
                        border-left:1px solid rgba(0,229,255,0.12);
                        z-index:1; pointer-events:none; overflow:hidden;
                      ">
                        <ha-camera-stream
                          .hass=\${hass}
                          .stateObj=\${hass.states['camera.parking_ezviz']}
                          style="width:100%;height:100%;object-fit:cover;opacity:0.45;">
                        </ha-camera-stream>
                      </div>`;
                    ]]]
                  b_bas:
                    card:
                      type: custom:button-card
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - padding: 0 6px
                          - height: 68px
                          - display: flex
                          - align-items: flex-end
                          - overflow: visible
                        grid:
                          - grid-template-columns: repeat(6, 1fr)
                          - grid-template-areas: '"b1 b2 b3 b4 b5 b6"'
                          - gap: 4px
                          - align-items: flex-end
                      custom_fields:
                        b1:
                          card:
                            type: custom:button-card
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: rgba(0,210,255,0.12)
                                - border: 1px solid rgba(0,210,255,0.45)
                                - border-top: none
                                - border-left: none
                                - box-shadow: >-
                                    2px 4px 14px rgba(0,210,255,0.18), inset 0
                                    -3px 0 #00d2ff
                                - border-radius: 0 0 8px 0
                                - height: 52px
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  const tmin = states['sensor.meteo_infos_detail']?.attributes?.temp_min_today;
                                  if (tmin === undefined) return '';
                                  return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;">
                                    <span style="font-family:Orbitron,sans-serif;font-size:15px;font-weight:700;color:#00d2ff;text-shadow:0 0 8px #00d2ff;">↓${tmin}°</span>
                                  </div>`;
                                ]]]
                        b2:
                          card:
                            type: custom:button-card
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: |
                                    [[[
                                      let t = states['sensor.vandeins_temperature']?.state;
                                      let hex = t >= 35 ? '#ff1744' : t >= 25 ? '#ffa500' : t >= 15 ? '#32cd32' : t >= 5 ? '#00e5ff' : '#7e57c2';
                                      return hex + '1c';
                                    ]]]
                                - border: |
                                    [[[
                                      let t = states['sensor.vandeins_temperature']?.state;
                                      let hex = t >= 35 ? '#ff1744' : t >= 25 ? '#ffa500' : t >= 15 ? '#32cd32' : t >= 5 ? '#00e5ff' : '#7e57c2';
                                      return '1px solid ' + hex + '72';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      let t = states['sensor.vandeins_temperature']?.state;
                                      let hex = t >= 35 ? '#ff1744' : t >= 25 ? '#ffa500' : t >= 15 ? '#32cd32' : t >= 5 ? '#00e5ff' : '#7e57c2';
                                      return '2px 4px 14px ' + hex + '2d, inset 0 -3px 0 ' + hex;
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: 52px
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  let t = states['sensor.vandeins_temperature']?.state;
                                  if (!t) return '';
                                  let color = t >= 35 ? '#ff1744' : t >= 25 ? 'orange' : t >= 15 ? 'limegreen' : t >= 5 ? '#00e5ff' : '#7e57c2';
                                  return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;">
                                    <span style="font-family:Orbitron,sans-serif;font-size:15px;font-weight:700;color:${color};text-shadow:0 0 8px ${color};">${t}°</span>
                                  </div>`;
                                ]]]
                        b3:
                          card:
                            type: custom:button-card
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: rgba(255,157,0,0.12)
                                - border: 1px solid rgba(255,157,0,0.45)
                                - border-top: none
                                - border-left: none
                                - box-shadow: >-
                                    2px 4px 14px rgba(255,157,0,0.18), inset 0
                                    -3px 0 #ff9d00
                                - border-radius: 0 0 8px 0
                                - height: 52px
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  const tmax = states['sensor.meteo_infos_detail']?.attributes?.temp_max_today;
                                  if (tmax === undefined) return '';
                                  return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;">
                                    <span style="font-family:Orbitron,sans-serif;font-size:15px;font-weight:700;color:#ff9d00;text-shadow:0 0 8px #ff9d00;">↑${tmax}°</span>
                                  </div>`;
                                ]]]
                        b4:
                          card:
                            type: custom:button-card
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: |
                                    [[[
                                      let v = states['sensor.vandeins_wind_speed']?.state;
                                      let hex = v >= 75 ? '#ff1744' : v >= 50 ? '#ffa500' : v >= 20 ? '#ffeb3b' : '#32cd32';
                                      return hex + '1c';
                                    ]]]
                                - border: |
                                    [[[
                                      let v = states['sensor.vandeins_wind_speed']?.state;
                                      let hex = v >= 75 ? '#ff1744' : v >= 50 ? '#ffa500' : v >= 20 ? '#ffeb3b' : '#32cd32';
                                      return '1px solid ' + hex + '72';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      let v = states['sensor.vandeins_wind_speed']?.state;
                                      let hex = v >= 75 ? '#ff1744' : v >= 50 ? '#ffa500' : v >= 20 ? '#ffeb3b' : '#32cd32';
                                      return '2px 4px 14px ' + hex + '2d, inset 0 -3px 0 ' + hex;
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: 52px
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  let v = states['sensor.vandeins_wind_speed']?.state;
                                  if (!v) return '';
                                  let color = v >= 75 ? '#ff1744' : v >= 50 ? 'orange' : v >= 20 ? '#ffeb3b' : 'limegreen';
                                  return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;">
                                    <span style="font-family:Orbitron,sans-serif;font-size:15px;font-weight:700;color:${color};text-shadow:0 0 8px ${color};">${v}km/h</span>
                                  </div>`;
                                ]]]
                        b5:
                          card:
                            type: custom:button-card
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: |
                                    [[[
                                      let state = states['sensor.meteo_alerte']?.state;
                                      let hex = '#32cd32';
                                      if (state && state.includes('Vigilance')) {
                                        hex = state.includes('jaune') ? '#ffeb3b' : state.includes('orange') ? '#ffa500' : '#ff1744';
                                      }
                                      return hex + '1c';
                                    ]]]
                                - border: |
                                    [[[
                                      let state = states['sensor.meteo_alerte']?.state;
                                      let hex = '#32cd32';
                                      if (state && state.includes('Vigilance')) {
                                        hex = state.includes('jaune') ? '#ffeb3b' : state.includes('orange') ? '#ffa500' : '#ff1744';
                                      }
                                      return '1px solid ' + hex + '72';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      let state = states['sensor.meteo_alerte']?.state;
                                      let hex = '#32cd32';
                                      if (state && state.includes('Vigilance')) {
                                        hex = state.includes('jaune') ? '#ffeb3b' : state.includes('orange') ? '#ffa500' : '#ff1744';
                                      }
                                      return '2px 4px 14px ' + hex + '2d, inset 0 -3px 0 ' + hex;
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: 52px
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  let state = states['sensor.meteo_alerte']?.state;
                                  let icon = 'mdi:thumb-up', color = 'limegreen', anim = 'none';
                                  if (state && state.includes('Vigilance')) {
                                    anim = 'blink 2 ease infinite';
                                    color = state.includes('jaune') ? '#ffeb3b' : state.includes('orange') ? 'orange' : '#ff1744';
                                    icon = state.includes('vent') ? 'mdi:weather-windy'
                                         : state.includes('neige') ? 'mdi:snowflake'
                                         : state.includes('froid') ? 'mdi:snowman'
                                         : state.includes('inondations') ? 'mdi:waves-arrow-up'
                                         : 'mdi:lightning-bolt';
                                  }
                                  return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;">
                                    <ha-icon icon="${icon}" style="width:22px;height:22px;color:${color};filter:drop-shadow(0 0 5px ${color});animation:${anim};"></ha-icon>
                                  </div>`;
                                ]]]
                        b6:
                          card:
                            type: custom:button-card
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: |
                                    [[[
                                      const cond = states['sensor.meteo_infos_detail']?.attributes?.etat_demain?.trim().toLowerCase();
                                      let hex = '#00e5ff';
                                      if (cond === 'sunny' || cond === 'partlycloudy') hex = '#ffeb3b';
                                      else if (cond === 'rainy' || cond === 'lightning-rainy' || cond === 'pouring') hex = '#448aff';
                                      else if (cond === 'cloudy' || cond === 'fog') hex = '#bdbdbd';
                                      else if (cond === 'snowy') hex = '#ffffff';
                                      return hex + '1c';
                                    ]]]
                                - border: |
                                    [[[
                                      const cond = states['sensor.meteo_infos_detail']?.attributes?.etat_demain?.trim().toLowerCase();
                                      let hex = '#00e5ff';
                                      if (cond === 'sunny' || cond === 'partlycloudy') hex = '#ffeb3b';
                                      else if (cond === 'rainy' || cond === 'lightning-rainy' || cond === 'pouring') hex = '#448aff';
                                      else if (cond === 'cloudy' || cond === 'fog') hex = '#bdbdbd';
                                      else if (cond === 'snowy') hex = '#ffffff';
                                      return '1px solid ' + hex + '72';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      const cond = states['sensor.meteo_infos_detail']?.attributes?.etat_demain?.trim().toLowerCase();
                                      let hex = '#00e5ff';
                                      if (cond === 'sunny' || cond === 'partlycloudy') hex = '#ffeb3b';
                                      else if (cond === 'rainy' || cond === 'lightning-rainy' || cond === 'pouring') hex = '#448aff';
                                      else if (cond === 'cloudy' || cond === 'fog') hex = '#bdbdbd';
                                      else if (cond === 'snowy') hex = '#ffffff';
                                      return '2px 4px 14px ' + hex + '2d, inset 0 -3px 0 ' + hex;
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: 52px
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  const cond = states['sensor.meteo_infos_detail']?.attributes?.etat_demain?.trim().toLowerCase();
                                  const icons = {
                                    'sunny':'mdi:white-balance-sunny','clear-night':'mdi:weather-night',
                                    'fog':'mdi:weather-fog','cloudy':'mdi:cloud',
                                    'partlycloudy':'mdi:weather-partly-cloudy','rainy':'mdi:weather-rainy',
                                    'lightning-rainy':'mdi:weather-lightning-rainy',
                                    'snowy':'mdi:snowflake','pouring':'mdi:weather-pouring'
                                  };
                                  let color = '#00e5ff', anim = 'none';
                                  if (cond === 'sunny' || cond === 'partlycloudy') color = '#ffeb3b';
                                  else if (cond === 'rainy' || cond === 'lightning-rainy' || cond === 'pouring') { color = '#448aff'; anim = 'pulse 2.5 infinite'; }
                                  else if (cond === 'cloudy' || cond === 'fog') color = '#bdbdbd';
                                  else if (cond === 'snowy') { color = 'white'; anim = 'pulse 3 infinite'; }
                                  const icon = icons[cond] || 'mdi:weather-cloudy';
                                  return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;">
                                    <ha-icon icon="${icon}" style="width:22px;height:22px;color:${color};filter:drop-shadow(0 0 5px ${color});animation:${anim};"></ha-icon>
                                  </div>`;
                                ]]]
                  DecoLines: |
                    [[[
                      const s = entity.state;
                      const night = variables.is_night;
                      let c = '#00e5ff';
                      if (night || s === 'clear-night') c = '#9575cd';
                      else if (s === 'sunny' || s === 'partlycloudy') c = '#ffeb3b';
                      else if (s === 'rainy' || s === 'lightning-rainy' || s === 'pouring') c = '#448aff';
                      else if (s === 'cloudy' || s === 'fog') c = '#bdbdbd';
                      const cOff = c + '30';
                      const divLine = c + '25';
                      const topLine = `linear-gradient(90deg,transparent,${c} 30%,${c} 70%,transparent)`;
                      return `
                        <style>
                          @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }
                          @keyframes pulse { 0%,100%{transform:scale(1)} 50%{transform:scale(1.1)} }
                          @keyframes drift { 0%,100%{transform:translateX(0px)} 50%{transform:translateX(5px)} }
                          @keyframes rotating { from{transform:rotate(0deg)} to{transform:rotate(360deg)} }
                        </style>
                        <div style="position:absolute;top:0;left:0;right:0;height:1px;background:${topLine};opacity:0.65;pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;bottom:68px;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,${divLine},transparent);pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;top:-1px;left:-1px;width:12px;height:12px;border-top:2px solid ${c};border-left:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;top:-1px;right:-1px;width:12px;height:12px;border-top:2px solid ${c};border-right:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;left:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-left:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;right:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-right:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                      `;
                    ]]]
              - type: custom:button-card
                entity: sensor.temperature_moyenne
                show_name: false
                show_state: false
                show_icon: false
                tap_action:
                  action: navigate
                  navigation_path: /lovelace/chauffage2
                hold_action:
                  action: navigate
                  navigation_path: /lovelace/chauffage
                styles:
                  card:
                    - height: 170px
                    - border-radius: 6px
                    - background: |
                        [[[
                          const t = parseFloat(entity.state);
                          if (t <= 15) return 'rgba(68,138,255,0.04)';
                          if (t >= 25) return 'rgba(255,23,68,0.04)';
                          return 'rgba(50,205,50,0.03)';
                        ]]]
                    - border: |
                        [[[
                          const t = parseFloat(entity.state);
                          if (t <= 15) return '1px solid rgba(68,138,255,0.4)';
                          if (t >= 25) return '1px solid rgba(255,23,68,0.45)';
                          return '1px solid rgba(50,205,50,0.35)';
                        ]]]
                    - box-shadow: |
                        [[[
                          const t = parseFloat(entity.state);
                          if (t <= 15) return '0 0 40px rgba(68,138,255,0.12)';
                          if (t >= 25) return '0 0 40px rgba(255,23,68,0.15)';
                          return '0 0 40px rgba(50,205,50,0.08)';
                        ]]]
                    - padding: 0
                    - overflow: visible
                    - position: relative
                  grid:
                    - grid-template-areas: '"ico_main titre_temp" "b_bas b_bas"'
                    - grid-template-columns: 95px 1fr
                    - grid-template-rows: 102px 68px
                  custom_fields:
                    ico_main:
                      - z-index: 2
                      - align-self: center
                      - justify-self: center
                    titre_temp:
                      - z-index: 2
                    b_bas:
                      - justify-self: stretch
                      - align-self: stretch
                      - z-index: 2
                    DecoLines:
                      - position: absolute
                      - top: 0
                      - left: 0
                      - width: 100%
                      - height: 100%
                      - pointer-events: none
                      - z-index: 5
                custom_fields:
                  ico_main: |
                    [[[
                      const t = parseFloat(entity.state);
                      let icon = 'mdi:thermometer';
                      let color = 'limegreen';
                      if (t <= 15) { icon = 'mdi:thermometer-minus'; color = '#448aff'; }
                      else if (t >= 25) { icon = 'mdi:thermometer-plus'; color = '#ff1744'; }
                      const cGlow = t <= 15 ? 'rgba(68,138,255' : t >= 25 ? 'rgba(255,23,68' : 'rgba(50,205,50';
                      return `<div style="
                        display:flex; align-items:center; justify-content:center;
                        width:95px; height:102px;
                        border-right:1px solid ${color}22;
                      ">
                        <ha-icon icon="${icon}" style="
                          width:70px; height:70px; color:${color};
                          filter: drop-shadow(0 0 12px ${cGlow},0.9)) drop-shadow(0 0 25px ${cGlow},0.4));
                        "></ha-icon>
                      </div>`;
                    ]]]
                  titre_temp: |
                    [[[
                      const t = parseFloat(entity.state);
                      let color = 'limegreen';
                      if (t <= 15) color = '#448aff';
                      else if (t >= 25) color = '#ff1744';
                      return `<div style="
                        display:flex; align-items:center; justify-content:center;
                        text-align:center; height:100%; width:100%;
                        font-family: Orbitron, sans-serif;
                        font-size: 32px;
                        font-weight: 700;
                        letter-spacing: 2px;
                        color: ${color};
                        text-shadow: 0 0 20px ${color}, 0 0 40px ${color}40;
                      ">${t}°</div>`;
                    ]]]
                  b_bas:
                    card:
                      type: custom:button-card
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - padding: 0 6px
                          - height: 68px
                          - display: flex
                          - align-items: flex-end
                          - overflow: visible
                        grid:
                          - grid-template-columns: repeat(6, 1fr)
                          - grid-template-areas: '"b1 b2 b3 b4 b5 b6"'
                          - gap: 4px
                          - align-items: flex-end
                      custom_fields:
                        b1:
                          card:
                            type: custom:button-card
                            entity: sensor.linky_prix_conso_jour_j_1
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: |
                                    [[[
                                      let val = parseFloat(states['sensor.linky_prix_conso_jour_j_1']?.state);
                                      let hex = val >= 12 ? '#ff1744' : val >= 10 ? '#ffa500' : val >= 7 ? '#ffeb3b' : '#32cd32';
                                      return hex + '1c';
                                    ]]]
                                - border: |
                                    [[[
                                      let val = parseFloat(states['sensor.linky_prix_conso_jour_j_1']?.state);
                                      let hex = val >= 12 ? '#ff1744' : val >= 10 ? '#ffa500' : val >= 7 ? '#ffeb3b' : '#32cd32';
                                      return '1px solid ' + hex + '72';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      let val = parseFloat(states['sensor.linky_prix_conso_jour_j_1']?.state);
                                      let hex = val >= 12 ? '#ff1744' : val >= 10 ? '#ffa500' : val >= 7 ? '#ffeb3b' : '#32cd32';
                                      return '2px 4px 14px ' + hex + '2d, inset 0 -3px 0 ' + hex;
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: 52px
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  let val = parseFloat(states['sensor.linky_prix_conso_jour_j_1']?.state);
                                  if (isNaN(val)) return '';
                                  let color = val >= 12 ? '#ff1744' : val >= 10 ? 'orange' : val >= 7 ? '#ffeb3b' : 'limegreen';
                                  return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;">
                                    <span style="font-family:Orbitron,sans-serif;font-size:16px;font-weight:700;color:${color};text-shadow:0 0 8px ${color};">${val}€</span>
                                  </div>`;
                                ]]]
                            tap_action:
                              action: navigate
                              navigation_path: /lovelace/energie
                        b2:
                          card:
                            type: custom:button-card
                            entity: sensor.linky_19986107005309_consumption
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: |
                                    [[[
                                      let val = parseFloat(states['sensor.linky_19986107005309_consumption']?.state);
                                      let hex = val >= 50 ? '#ff1744' : val >= 40 ? '#ffa500' : val >= 30 ? '#ffeb3b' : '#32cd32';
                                      return hex + '1c';
                                    ]]]
                                - border: |
                                    [[[
                                      let val = parseFloat(states['sensor.linky_19986107005309_consumption']?.state);
                                      let hex = val >= 50 ? '#ff1744' : val >= 40 ? '#ffa500' : val >= 30 ? '#ffeb3b' : '#32cd32';
                                      return '1px solid ' + hex + '72';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      let val = parseFloat(states['sensor.linky_19986107005309_consumption']?.state);
                                      let hex = val >= 50 ? '#ff1744' : val >= 40 ? '#ffa500' : val >= 30 ? '#ffeb3b' : '#32cd32';
                                      return '2px 4px 14px ' + hex + '2d, inset 0 -3px 0 ' + hex;
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: 52px
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  let val = parseFloat(states['sensor.linky_19986107005309_consumption']?.state);
                                  if (isNaN(val)) return '';
                                  let color = val >= 50 ? '#ff1744' : val >= 40 ? 'orange' : val >= 30 ? '#ffeb3b' : 'limegreen';
                                  return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;">
                                    <span style="font-family:Orbitron,sans-serif;font-size:15px;font-weight:700;color:${color};text-shadow:0 0 8px ${color};">${Math.round(val)}kWh</span>
                                  </div>`;
                                ]]]
                            tap_action:
                              action: navigate
                              navigation_path: /lovelace/energie
                        b3:
                          card:
                            type: custom:button-card
                            entity: sensor.etat_du_chauffage
                            show_name: false
                            show_state: false
                            icon: |
                              [[[
                                const s = states['sensor.etat_du_chauffage']?.state;
                                if (s === 'Hors Gel') return 'tc:frost-point';
                                if (s === 'Éco') return 'mdi:leaf';
                                if (s === 'Confort') return 'mdi:sofa';
                                if (s === 'Chauffe') return 'mdi:fire';
                                if (s === 'Boost') return 'mdi:rocket-launch';
                                if (s === 'Mode Éte') return 'mdi:sun-snowflake-variant';
                                return 'mdi:power';
                              ]]]
                            styles:
                              icon:
                                - color: |
                                    [[[
                                      const s = states['sensor.etat_du_chauffage']?.state;
                                      if (s === 'Hors Gel') return '#00e5ff';
                                      if (s === 'Éco') return '#32cd32';
                                      if (s === 'Confort' || s === 'Chauffe') return '#ffc107';
                                      if (s === 'Boost') return '#ff1744';
                                      if (s === 'Mode Éte') return '#448aff';
                                      return '#00e5ff';
                                    ]]]
                                - filter: |
                                    [[[
                                      const s = states['sensor.etat_du_chauffage']?.state;
                                      if (s === 'Boost') return 'drop-shadow(0 0 6px rgba(255,23,68,0.8))';
                                      if (s === 'Chauffe' || s === 'Confort') return 'drop-shadow(0 0 6px rgba(255,193,7,0.8))';
                                      return 'none';
                                    ]]]
                              card:
                                - background: |
                                    [[[
                                      const s = states['sensor.etat_du_chauffage']?.state;
                                      let hex = '#00e5ff';
                                      if (s === 'Hors Gel') hex = '#00e5ff';
                                      else if (s === 'Éco') hex = '#32cd32';
                                      else if (s === 'Confort' || s === 'Chauffe') hex = '#ffc107';
                                      else if (s === 'Boost') hex = '#ff1744';
                                      else if (s === 'Mode Éte') hex = '#448aff';
                                      return hex + '1c';
                                    ]]]
                                - border: |
                                    [[[
                                      const s = states['sensor.etat_du_chauffage']?.state;
                                      let hex = '#00e5ff';
                                      if (s === 'Hors Gel') hex = '#00e5ff';
                                      else if (s === 'Éco') hex = '#32cd32';
                                      else if (s === 'Confort' || s === 'Chauffe') hex = '#ffc107';
                                      else if (s === 'Boost') hex = '#ff1744';
                                      else if (s === 'Mode Éte') hex = '#448aff';
                                      return '1px solid ' + hex + '72';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      const s = states['sensor.etat_du_chauffage']?.state;
                                      let hex = '#00e5ff';
                                      if (s === 'Hors Gel') hex = '#00e5ff';
                                      else if (s === 'Éco') hex = '#32cd32';
                                      else if (s === 'Confort' || s === 'Chauffe') hex = '#ffc107';
                                      else if (s === 'Boost') hex = '#ff1744';
                                      else if (s === 'Mode Éte') hex = '#448aff';
                                      return '2px 4px 14px ' + hex + '2d, inset 0 -3px 0 ' + hex;
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: 52px
                            tap_action:
                              action: navigate
                              navigation_path: /lovelace/chauffage2
                            hold_action:
                              action: navigate
                              navigation_path: /lovelace/poele
                        b4:
                          card:
                            type: custom:button-card
                            entity: input_boolean.mode_absence
                            show_name: false
                            show_state: false
                            icon: mdi:text-long
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: toggle
                              confirmation:
                                text: Activer / Désactiver le mode Absence longue ?
                        b5:
                          card:
                            type: custom:button-card
                            entity: input_boolean.chauffage_mode_vacances
                            show_name: false
                            show_state: false
                            icon: mdi:home-account
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: toggle
                              confirmation:
                                text: Activer / Désactiver le Mode Vacances ?
                        b6:
                          card:
                            type: custom:button-card
                            entity: input_boolean.mode_absence_courte_duree
                            show_name: false
                            show_state: false
                            icon: mdi:text-short
                            state:
                              - value: 'on'
                                styles:
                                  icon:
                                    - color: '#ffc107'
                                    - filter: drop-shadow(0 0 6px rgba(255,193,7,0.8))
                                  card:
                                    - background: rgba(255,193,7,0.12)
                                    - border: 1px solid rgba(255,193,7,0.45)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: >-
                                        2px 4px 14px rgba(255,193,7,0.18), inset
                                        0 -3px 0 #ffc107
                                    - border-radius: 0 0 8px 0
                                    - height: 52px
                              - value: 'off'
                                styles:
                                  icon:
                                    - color: rgba(0,229,255,0.35)
                                    - filter: none
                                  card:
                                    - background: rgba(0,10,22,0.85)
                                    - border: 1px solid rgba(0,229,255,0.13)
                                    - border-top: none
                                    - border-left: none
                                    - box-shadow: none
                                    - border-radius: 0 0 8px 0
                                    - height: 42px
                            tap_action:
                              action: toggle
                              confirmation:
                                text: Activer / Désactiver le Mode Absence Courte ?
                  DecoLines: |
                    [[[
                      const t = parseFloat(entity.state);
                      const c = t <= 15 ? '#448aff' : t >= 25 ? '#ff1744' : 'limegreen';
                      const cOff = t <= 15 ? 'rgba(68,138,255,0.18)' : t >= 25 ? 'rgba(255,23,68,0.18)' : 'rgba(50,205,50,0.18)';
                      const divLine = t <= 15 ? 'rgba(68,138,255,0.15)' : t >= 25 ? 'rgba(255,23,68,0.15)' : 'rgba(50,205,50,0.12)';
                      const topLine = `linear-gradient(90deg,transparent,${c} 30%,${c} 70%,transparent)`;
                      return `
                        <div style="position:absolute;top:0;left:0;right:0;height:1px;background:${topLine};opacity:0.65;pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;bottom:68px;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,${divLine},transparent);pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;top:-1px;left:-1px;width:12px;height:12px;border-top:2px solid ${c};border-left:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;top:-1px;right:-1px;width:12px;height:12px;border-top:2px solid ${c};border-right:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;left:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-left:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;right:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-right:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                      `;
                    ]]]
              - type: custom:button-card
                entity: media_player.medias
                show_name: false
                show_state: false
                show_icon: false
                variables:
                  mode: |
                    [[[
                      const applipop = states['sensor.applipop']?.state;
                      const appli_pop = states['sensor.appli_pop']?.state;
                      const pop_nom_appli = states['sensor.pop_nom_appli']?.state;
                      const blu = states['media_player.blu_ray']?.state;
                      const tv2 = states['media_player.tv_salon_2']?.state;
                      const tv3 = states['media_player.tv_salon_3']?.state;
                      const ps5_online = states['sensor.ps5_online_status']?.state;
                      const ps5_activity = states['sensor.ps5_073_activity']?.state;
                      const switch_power = Number(states['sensor.prise_nintendo_switch_active_power_3']?.state);

                      if (applipop === 'Netflix' && blu !== 'idle') return 'netflix';
                      if (applipop === 'Canal+' && blu !== 'idle') return 'canal';
                      if (applipop === 'VLC' && blu !== 'idle') return 'vlc';
                      if (applipop === 'Amazon Music' && blu !== 'idle') return 'amazon_music';
                      if (applipop === 'Free TV' && blu !== 'idle' && tv2 === 'on' && ps5_online !== 'availabletoplay' && switch_power < 5) return 'free_tv';
                      if (applipop === 'Photos' && blu !== 'idle' && tv3 === 'on' && switch_power < 5 && ps5_activity !== 'idle') return 'photos';
                      if (applipop === 'Disney+' && blu !== 'idle') return 'disney_plus';
                      if (applipop === 'YouTube' && blu !== 'idle') return 'youtube';
                      if (applipop === 'M6+' && blu !== 'idle' && ps5_activity !== 'idle') return 'm6_plus_ps5';
                      if (appli_pop === 'Spotify' && blu !== 'idle') return 'spotify';
                      if (appli_pop === 'Music Assistant') return 'music_assistant';
                      if (pop_nom_appli === 'home' && blu !== 'idle') return 'free_foot';
                      if (pop_nom_appli === 'com.google.android.apps.mediashell' && blu !== 'idle') return 'xalaflix';
                      if (blu === 'idle') return 'bluray_idle';
                      if (tv2 === 'on' && switch_power > 5) return 'switch';
                      if (ps5_online === 'availabletoplay') return 'ps5_online';
                      return 'default';
                    ]]]
                tap_action:
                  action: fire-dom-event
                  honeycomb_menu:
                    autoclose: true
                    size: 210
                    filter_radius: 100
                    style:
                      - background: rgba(0,5,15,0.92)
                      - border: 1px solid rgba(0,229,255,0.25)
                      - box-shadow: 0 0 40px rgba(0,229,255,0.1)
                      - border-radius: 6px
                    buttons:
                      - icon: mdi:power
                        entity: media_player.android_tv_192_168_1_9
                        styles:
                          icon:
                            - color: '#ff1744'
                          card:
                            - background: rgba(255,23,68,0.1)
                            - border: 1px solid rgba(255,23,68,0.3)
                        tap_action:
                          action: call-service
                          service: androidtv.adb_command
                          service_data:
                            entity_id: media_player.android_tv_192_168_1_9
                            command: POWER
                      - icon: mdi:plus
                        entity: media_player.android_tv_192_168_1_9
                        styles:
                          icon:
                            - color: '#00e5ff'
                          card:
                            - background: rgba(0,229,255,0.08)
                            - border: 1px solid rgba(0,229,255,0.25)
                        tap_action:
                          action: call-service
                          service: androidtv.adb_command
                          service_data:
                            entity_id: media_player.android_tv_192_168_1_9
                            command: CHANNEL_UP
                      - icon: mdi:volume-plus
                        entity: media_player.android_tv_192_168_1_9
                        styles:
                          icon:
                            - color: '#00e5ff'
                          card:
                            - background: rgba(0,229,255,0.08)
                            - border: 1px solid rgba(0,229,255,0.25)
                        tap_action:
                          action: call-service
                          service: androidtv.adb_command
                          service_data:
                            entity_id: media_player.android_tv_192_168_1_9
                            command: VOLUME_UP
                      - icon: mdi:television
                        entity: light.prise_tv_switch
                        styles:
                          icon:
                            - color: '#ffc107'
                          card:
                            - background: rgba(255,193,7,0.08)
                            - border: 1px solid rgba(255,193,7,0.25)
                        tap_action:
                          action: call-service
                          service: light.turn_on
                          service_data:
                            entity_id: light.prise_tv_switch
                        hold_action:
                          action: call-service
                          service: light.turn_off
                          service_data:
                            entity_id: light.prise_tv_switch
                      - icon: mdi:minus
                        entity: media_player.android_tv_192_168_1_9
                        styles:
                          icon:
                            - color: '#00e5ff'
                          card:
                            - background: rgba(0,229,255,0.08)
                            - border: 1px solid rgba(0,229,255,0.25)
                        tap_action:
                          action: call-service
                          service: androidtv.adb_command
                          service_data:
                            entity_id: media_player.android_tv_192_168_1_9
                            command: CHANNEL_DOWN
                      - icon: mdi:volume-minus
                        entity: media_player.android_tv_192_168_1_9
                        styles:
                          icon:
                            - color: '#00e5ff'
                          card:
                            - background: rgba(0,229,255,0.08)
                            - border: 1px solid rgba(0,229,255,0.25)
                        tap_action:
                          action: call-service
                          service: androidtv.adb_command
                          service_data:
                            entity_id: media_player.android_tv_192_168_1_9
                            command: VOLUME_DOWN
                hold_action:
                  action: navigate
                  navigation_path: /lovelace/telecommande
                double_tap_action:
                  action: fire-dom-event
                  browser_mod:
                    service: browser_mod.popup
                    data:
                      title: Programme TV
                      content:
                        type: custom:programme-tnt-fr-card
                extra_styles: >
                  @keyframes scrolling { 0%{transform:translateX(100%)}
                  100%{transform:translateX(-100%)} }

                  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }
                styles:
                  card:
                    - height: 170px
                    - border-radius: 6px
                    - background: |
                        [[[
                          const colors = {
                            netflix:'rgba(229,9,20,0.04)', canal:'rgba(229,9,20,0.04)',
                            vlc:'rgba(255,153,0,0.04)', amazon_music:'rgba(0,168,255,0.04)',
                            spotify:'rgba(30,215,96,0.04)', free_tv:'rgba(229,9,20,0.04)',
                            photos:'rgba(0,229,255,0.04)', free_foot:'rgba(255,255,255,0.03)',
                            bluray_idle:'rgba(68,138,255,0.04)', music_assistant:'rgba(255,255,255,0.03)',
                            xalaflix:'rgba(148,0,211,0.04)', disney_plus:'rgba(68,138,255,0.04)',
                            youtube:'rgba(229,9,20,0.04)', switch:'rgba(230,0,18,0.04)',
                            m6_plus_ps5:'rgba(255,235,59,0.04)', ps5_online:'rgba(68,138,255,0.04)',
                          };
                          return colors[variables.mode] || 'rgba(0,229,255,0.03)';
                        ]]]
                    - border: |
                        [[[
                          const colors = {
                            netflix:'rgba(229,9,20,0.5)', canal:'rgba(229,9,20,0.5)',
                            vlc:'rgba(255,153,0,0.45)', amazon_music:'rgba(0,168,255,0.35)',
                            spotify:'rgba(30,215,96,0.4)', free_tv:'rgba(229,9,20,0.5)',
                            photos:'rgba(0,229,255,0.3)', free_foot:'rgba(255,255,255,0.25)',
                            bluray_idle:'rgba(68,138,255,0.4)', music_assistant:'rgba(255,255,255,0.25)',
                            xalaflix:'rgba(148,0,211,0.5)', disney_plus:'rgba(68,138,255,0.4)',
                            youtube:'rgba(229,9,20,0.5)', switch:'rgba(230,0,18,0.4)',
                            m6_plus_ps5:'rgba(255,235,59,0.45)', ps5_online:'rgba(68,138,255,0.4)',
                          };
                          return `1px solid ${colors[variables.mode] || 'rgba(0,229,255,0.2)'}`;
                        ]]]
                    - box-shadow: |
                        [[[
                          const colors = {
                            netflix:'rgba(229,9,20,0.2)', canal:'rgba(229,9,20,0.2)',
                            vlc:'rgba(255,153,0,0.15)', amazon_music:'rgba(0,168,255,0.12)',
                            spotify:'rgba(30,215,96,0.15)', free_tv:'rgba(229,9,20,0.2)',
                            photos:'rgba(0,229,255,0.1)', free_foot:'rgba(255,255,255,0.08)',
                            bluray_idle:'rgba(68,138,255,0.12)', music_assistant:'rgba(255,255,255,0.08)',
                            xalaflix:'rgba(148,0,211,0.2)', disney_plus:'rgba(68,138,255,0.12)',
                            youtube:'rgba(229,9,20,0.2)', switch:'rgba(230,0,18,0.12)',
                            m6_plus_ps5:'rgba(255,235,59,0.15)', ps5_online:'rgba(68,138,255,0.12)',
                          };
                          return `0 0 40px ${colors[variables.mode] || 'rgba(0,229,255,0.06)'}`;
                        ]]]
                    - padding: 0
                    - overflow: visible
                    - position: relative
                  grid:
                    - grid-template-areas: '"ico_main titre_media" "b_bas b_bas"'
                    - grid-template-columns: 95px 1fr
                    - grid-template-rows: 102px 68px
                  custom_fields:
                    ico_main:
                      - z-index: 2
                      - align-self: center
                      - justify-self: center
                    titre_media:
                      - z-index: 2
                    b_bas:
                      - justify-self: stretch
                      - align-self: stretch
                      - z-index: 2
                    DecoLines:
                      - position: absolute
                      - top: 0
                      - left: 0
                      - width: 100%
                      - height: 100%
                      - pointer-events: none
                      - z-index: 5
                custom_fields:
                  ico_main: |
                    [[[
                      const mode = variables.mode;
                      const modeColors = {
                        netflix:'#e5091b', canal:'#e5091b', vlc:'#ff9900',
                        amazon_music:'#00a8ff', spotify:'#1ed760', free_tv:'#e5091b',
                        photos:'#00e5ff', free_foot:'#ffffff', bluray_idle:'#448aff',
                        music_assistant:'#ffffff', xalaflix:'#9400d3', disney_plus:'#448aff',
                        youtube:'#e5091b', switch:'#e6001a', m6_plus_ps5:'#ffeb3b',
                        ps5_online:'#448aff', default:'#00e5ff'
                      };
                      const color = modeColors[mode] || '#00e5ff';

                      const icons = {
                        netflix: 'phu:netflix', canal: 'mdi:television-classic',
                        vlc: 'mdi:cone', amazon_music: 'phu:amazon-music',
                        spotify: 'mdi:spotify', free_tv: 'cil:tv',
                        photos: 'mdi:panorama-variant-outline', free_foot: 'mdi:soccer-field',
                        bluray_idle: 'mdi:disc-player', music_assistant: 'mdi:music',
                        xalaflix: 'mdi:alpha-x', disney_plus: 'phu:disney-plus',
                        youtube: 'mdi:youtube', switch: 'mdi:nintendo-switch',
                        m6_plus_ps5: 'mdi:television', ps5_online: 'phu:playstation-5',
                        default: 'mdi:television'
                      };

                      // PS5 : pochette de jeu
                      if (mode === 'ps5_online') {
                        const img = states['image.ps5_lecture_en_cours']?.attributes?.entity_picture;
                        if (img) return `<div style="
                          display:flex; align-items:center; justify-content:center;
                          width:95px; height:102px;
                          border-right:1px solid rgba(68,138,255,0.2);
                        "><img src="${img}" style="width:75px;height:75px;object-fit:contain;border-radius:6px;
                          filter:drop-shadow(0 0 8px rgba(68,138,255,0.6));"></div>`;
                      }

                      // M6+ : logo image
                      if (mode === 'm6_plus_ps5') {
                        return `<div style="
                          display:flex; align-items:center; justify-content:center;
                          width:95px; height:102px;
                          border-right:1px solid rgba(255,235,59,0.2);
                        "><img src="https://m.media-amazon.com/images/I/31GHJjUDDRL.png"
                          style="width:70px;height:70px;object-fit:contain;
                          filter:drop-shadow(0 0 8px rgba(255,235,59,0.6));"></div>`;
                      }

                      return `<div style="
                        display:flex; align-items:center; justify-content:center;
                        width:95px; height:102px;
                        border-right:1px solid ${color}33;
                      ">
                        <ha-icon icon="${icons[mode] || icons.default}" style="
                          width:70px; height:70px; color:${color};
                          filter: drop-shadow(0 0 12px ${color}dd) drop-shadow(0 0 25px ${color}66);
                        "></ha-icon>
                      </div>`;
                    ]]]
                  titre_media: |
                    [[[
                      const mode = variables.mode;
                      const artist = states['sensor.pop_nom_artiste']?.state || '';
                      const song = states['sensor.pop_nom_chanson']?.state || '';
                      const jeu = states['sensor.ps5_lecture_en_cours']?.state;
                      const nomJeu = (!jeu || jeu === 'unavailable' || jeu === 'unknown') ? 'PlayStation 5' : jeu;

                      const modeColors = {
                        netflix:'#e5091b', canal:'#e5091b', vlc:'#ff9900',
                        amazon_music:'#00a8ff', spotify:'#1ed760', free_tv:'#e5091b',
                        photos:'#00e5ff', free_foot:'#ffffff', bluray_idle:'#448aff',
                        music_assistant:'#ffffff', xalaflix:'#9400d3', disney_plus:'#448aff',
                        youtube:'#e5091b', switch:'#e6001a', m6_plus_ps5:'#ffeb3b',
                        ps5_online:'#448aff', default:'#00e5ff'
                      };
                      const color = modeColors[mode] || '#00e5ff';

                      const scroll = (text, c) => `
                        <div style="overflow:hidden;white-space:nowrap;width:100%;">
                          <span style="display:inline-block;animation:scrolling 10s linear infinite;
                            font-family:Inter,sans-serif;font-size:14px;color:${c};">${text}</span>
                        </div>`;

                      const staticLabel = (label, c, size='32px') => `
                        <span style="font-family:Orbitron,sans-serif;font-size:${size};font-weight:700;
                          letter-spacing:2px;color:${c};text-shadow:0 0 15px ${c},0 0 30px ${c}66;
                          white-space:nowrap;">${label}</span>`;

                      let topLine = '';
                      let bottomLine = '';

                      if (['amazon_music','spotify','music_assistant','photos','youtube','m6_plus_ps5','xalaflix'].includes(mode)) {
                        topLine = artist ? scroll(artist, '#ffffff') : staticLabel('Médias', color);
                        bottomLine = song ? scroll(song, color) : '';
                      } else if (mode === 'ps5_online') {
                        topLine = staticLabel('PlayStation 5', color);
                        bottomLine = `<span style="font-family:Inter,sans-serif;font-size:13px;color:${color};opacity:0.8;">${nomJeu}</span>`;
                      } else if (mode === 'free_tv') {
                        topLine = `<img src="https://tv.free.fr/landing/images/Logo-FreeTV.svg" style="height:28px;filter:drop-shadow(0 0 6px ${color});">`;
                        bottomLine = song ? scroll(song, color) : '';
                      } else if (mode === 'disney_plus') {
                        topLine = `<img src="https://logodownload.org/wp-content/uploads/2020/11/disney-plus-logo.png" style="height:28px;filter:drop-shadow(0 0 6px ${color});">`;
                      } else if (mode === 'bluray_idle') {
                        topLine = staticLabel('Blu-Ray', color);
                        bottomLine = `<ha-icon icon="phu:bluray" style="width:32px;color:${color};filter:drop-shadow(0 0 6px ${color});"></ha-icon>`;
                      } else {
                        const labels = {
                          netflix:'Netflix', canal:'Canal+', vlc:'VLC',
                          free_foot:'Free Foot', switch:'Nintendo Switch', default:'Multimédias'
                        };
                        topLine = staticLabel(labels[mode] || labels.default, color);
                      }

                      return `<div style="
                        display:flex; flex-direction:column; align-items:center; justify-content:center;
                        height:100%; width:100%; gap:6px; padding:0 8px;
                      ">${topLine}${bottomLine ? bottomLine : ''}</div>`;
                    ]]]
                  b_bas: |
                    [[[
                      const mode = variables.mode;
                      const modeColors = {
                        netflix:'#e5091b', canal:'#e5091b', vlc:'#ff9900',
                        amazon_music:'#00a8ff', spotify:'#1ed760', free_tv:'#e5091b',
                        photos:'#00e5ff', free_foot:'#ffffff', bluray_idle:'#448aff',
                        music_assistant:'#ffffff', xalaflix:'#9400d3', disney_plus:'#448aff',
                        youtube:'#e5091b', switch:'#e6001a', m6_plus_ps5:'#ffeb3b',
                        ps5_online:'#448aff', default:'#00e5ff'
                      };
                      const c = modeColors[mode] || '#00e5ff';
                      const cRgb = {
                        netflix:'229,9,27', canal:'229,9,27', vlc:'255,153,0',
                        amazon_music:'0,168,255', spotify:'30,215,96', free_tv:'229,9,27',
                        photos:'0,229,255', free_foot:'255,255,255', bluray_idle:'68,138,255',
                        music_assistant:'255,255,255', xalaflix:'148,0,211', disney_plus:'68,138,255',
                        youtube:'229,9,27', switch:'230,0,18', m6_plus_ps5:'255,235,59',
                        ps5_online:'68,138,255', default:'0,229,255'
                      };
                      const rgb = cRgb[mode] || '0,229,255';

                      const tab = (icon, active=false) => `<div style="
                        flex:1; height:${active ? '52' : '42'}px;
                        display:flex; align-items:center; justify-content:center;
                        background:${active ? `rgba(${rgb},0.12)` : 'rgba(0,10,22,0.85)'};
                        border:1px solid ${active ? `rgba(${rgb},0.45)` : `rgba(${rgb},0.15)`};
                        border-top:none; border-left:none;
                        border-radius:0 0 8px 0;
                        ${active ? `box-shadow:2px 4px 14px rgba(${rgb},0.18),inset 0 -3px 0 ${c};` : ''}
                      ">
                        <ha-icon icon="${icon}" style="width:18px;height:18px;color:${active ? c : `rgba(${rgb},0.35)`};
                          ${active ? `filter:drop-shadow(0 0 5px ${c});` : ''}"></ha-icon>
                      </div>`;

                      const isTv = ['netflix','canal','free_tv','disney_plus','youtube','free_foot','xalaflix','m6_plus_ps5'].includes(mode);
                      const isMusic = ['spotify','amazon_music','music_assistant'].includes(mode);
                      const isGame = ['ps5_online','switch'].includes(mode);
                      const isBlu = mode === 'bluray_idle';
                      const isPhotos = mode === 'photos';

                      return `<div style="
                        display:flex; align-items:flex-end; gap:4px;
                        height:68px; padding:0 6px; overflow:visible;
                      ">
                        ${tab('mdi:television', isTv)}
                        ${tab('mdi:music', isMusic)}
                        ${tab('mdi:disc-player', isBlu)}
                        ${tab('mdi:gamepad-variant', isGame)}
                        ${tab('mdi:panorama-variant-outline', isPhotos)}
                        ${tab('mdi:dots-horizontal', false)}
                      </div>`;
                    ]]]
                  DecoLines: |
                    [[[
                      const mode = variables.mode;
                      const modeColors = {
                        netflix:'#e5091b', canal:'#e5091b', vlc:'#ff9900',
                        amazon_music:'#00a8ff', spotify:'#1ed760', free_tv:'#e5091b',
                        photos:'#00e5ff', free_foot:'#ffffff', bluray_idle:'#448aff',
                        music_assistant:'#ffffff', xalaflix:'#9400d3', disney_plus:'#448aff',
                        youtube:'#e5091b', switch:'#e6001a', m6_plus_ps5:'#ffeb3b',
                        ps5_online:'#448aff', default:'#00e5ff'
                      };
                      const c = modeColors[mode] || '#00e5ff';
                      const cOff = c + '30';
                      const divLine = c + '22';
                      const topLine = `linear-gradient(90deg,transparent,${c} 30%,${c} 70%,transparent)`;
                      return `
                        <div style="position:absolute;top:0;left:0;right:0;height:1px;background:${topLine};opacity:0.65;pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;bottom:68px;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,${divLine},transparent);pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;top:-1px;left:-1px;width:12px;height:12px;border-top:2px solid ${c};border-left:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;top:-1px;right:-1px;width:12px;height:12px;border-top:2px solid ${c};border-right:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;left:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-left:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;right:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-right:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                      `;
                    ]]]
              - type: custom:button-card
                entity: light.prise_tv_switch
                show_name: false
                show_state: false
                show_icon: false
                triggers_update: all
                tap_action:
                  action: navigate
                  navigation_path: /lovelace/ha
                extra_styles: |
                  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }
                styles:
                  card:
                    - height: 170px
                    - border-radius: 6px
                    - background: |
                        [[[
                          const isHome = states['device_tracker.homeassistant']?.state === 'home';
                          return isHome ? 'rgba(50,205,50,0.03)' : 'rgba(0,229,255,0.03)';
                        ]]]
                    - border: |
                        [[[
                          const isHome = states['device_tracker.homeassistant']?.state === 'home';
                          if (entity.state === 'on' || entity.state === 'off') {
                            return isHome ? '1px solid rgba(50,205,50,0.35)' : '1px solid rgba(0,229,255,0.2)';
                          }
                          return '1px solid rgba(255,193,7,0.4)';
                        ]]]
                    - box-shadow: |
                        [[[
                          const isHome = states['device_tracker.homeassistant']?.state === 'home';
                          if (entity.state === 'on' || entity.state === 'off') {
                            return isHome ? '0 0 40px rgba(50,205,50,0.08)' : '0 0 40px rgba(0,229,255,0.06)';
                          }
                          return '0 0 40px rgba(255,193,7,0.1)';
                        ]]]
                    - padding: 0
                    - overflow: visible
                    - position: relative
                  grid:
                    - grid-template-areas: '"ico_main titre_sys" "b_bas b_bas"'
                    - grid-template-columns: 95px 1fr
                    - grid-template-rows: 102px 68px
                  custom_fields:
                    ico_main:
                      - z-index: 2
                      - align-self: center
                      - justify-self: center
                    titre_sys:
                      - z-index: 2
                    b_bas:
                      - justify-self: stretch
                      - align-self: stretch
                      - z-index: 2
                    DecoLines:
                      - position: absolute
                      - top: 0
                      - left: 0
                      - width: 100%
                      - height: 100%
                      - pointer-events: none
                      - z-index: 5
                custom_fields:
                  ico_main: |
                    [[[
                      const isHome = states['device_tracker.homeassistant']?.state === 'home';
                      const ok = entity.state === 'on' || entity.state === 'off';
                      const color = ok ? (isHome ? 'limegreen' : '#00e5ff') : '#ffc107';
                      const cGlow = ok ? (isHome ? 'rgba(50,205,50' : 'rgba(0,229,255') : 'rgba(255,193,7';
                      return `<div style="
                        display:flex; align-items:center; justify-content:center;
                        width:95px; height:102px;
                        border-right:1px solid ${color}22;
                      ">
                        <ha-icon icon="mdi:home-assistant" style="
                          width:70px; height:70px; color:${color};
                          filter: drop-shadow(0 0 12px ${cGlow},0.9)) drop-shadow(0 0 25px ${cGlow},0.4));
                        "></ha-icon>
                      </div>`;
                    ]]]
                  titre_sys: |
                    [[[
                      const isHome = states['device_tracker.homeassistant']?.state === 'home';
                      const ok = entity.state === 'on' || entity.state === 'off';
                      const color = ok ? (isHome ? 'limegreen' : '#00e5ff') : '#ffc107';
                      return `<div style="
                        display:flex; align-items:center; justify-content:center;
                        text-align:center; height:100%; width:100%;
                        font-family: Orbitron, sans-serif;
                        font-size: 32px;
                        font-weight: 700;
                        letter-spacing: 3px;
                        color: ${color};
                        text-shadow: 0 0 20px ${color}, 0 0 40px ${color}40;
                        white-space: nowrap;
                      ">Système</div>`;
                    ]]]
                  b_bas:
                    card:
                      type: custom:button-card
                      styles:
                        card:
                          - background: none
                          - border: none
                          - box-shadow: none
                          - padding: 0 6px
                          - height: 68px
                          - display: flex
                          - align-items: flex-end
                          - overflow: visible
                        grid:
                          - grid-template-columns: repeat(6, 1fr)
                          - grid-template-areas: '"b1 b2 b3 b4 b5 b6"'
                          - gap: 4px
                          - align-items: flex-end
                      custom_fields:
                        b1:
                          card:
                            type: custom:button-card
                            entity: sensor.nombre_de_mises_a_jour
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: |
                                    [[[
                                      let n = parseInt(states['sensor.nombre_de_mises_a_jour']?.state);
                                      let hasUpdate = !isNaN(n) && n > 0;
                                      let hex = hasUpdate ? '#ffc107' : 'rgba(0,10,22,0.85)';
                                      return hasUpdate ? hex + '1c' : hex;
                                    ]]]
                                - border: |
                                    [[[
                                      let n = parseInt(states['sensor.nombre_de_mises_a_jour']?.state);
                                      let hasUpdate = !isNaN(n) && n > 0;
                                      return hasUpdate ? '1px solid #ffc10772' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      let n = parseInt(states['sensor.nombre_de_mises_a_jour']?.state);
                                      let hasUpdate = !isNaN(n) && n > 0;
                                      return hasUpdate ? '2px 4px 14px #ffc1072d, inset 0 -3px 0 #ffc107' : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      let n = parseInt(states['sensor.nombre_de_mises_a_jour']?.state);
                                      let hasUpdate = !isNaN(n) && n > 0;
                                      return hasUpdate ? '52px' : '42px';
                                    ]]]
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  let n = parseInt(states['sensor.nombre_de_mises_a_jour']?.state);
                                  let hasUpdate = !isNaN(n) && n > 0;
                                  if (!hasUpdate) {
                                    return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;">
                                      <ha-icon icon="mdi:update" style="width:20px;height:20px;color:rgba(0,229,255,0.35);filter:none;"></ha-icon>
                                    </div>`;
                                  }
                                  let color = '#ffc107';
                                  return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;gap:4px;">
                                    <ha-icon icon="mdi:update" style="width:20px;height:20px;color:${color};filter:drop-shadow(0 0 6px ${color});"></ha-icon>
                                    <span style="font-family:Orbitron,sans-serif;font-size:14px;font-weight:700;color:${color};text-shadow:0 0 8px ${color};">${n}</span>
                                  </div>`;
                                ]]]
                            tap_action:
                              action: fire-dom-event
                              browser_mod:
                                service: browser_mod.popup
                                data:
                                  title: MISES À JOUR
                                  style: >
                                    --popup-background: rgba(0, 5, 15, 0.96);
                                    --popup-border-radius: 6px;
                                    --popup-border-color: rgba(0,229,255,0.22);
                                    --popup-box-shadow: 0 0 40px
                                    rgba(0,229,255,0.08); --ha-card-background:
                                    transparent; --mdc-theme-surface: rgba(0, 5,
                                    15, 0.96); --primary-text-color: #00e5ff;
                                    --secondary-text-color:
                                    rgba(0,229,255,0.55); --mdc-theme-primary:
                                    #00e5ff; backdrop-filter: blur(12px);
                                  content:
                                    type: vertical-stack
                                    cards:
                                      - type: custom:button-card
                                        entity: script.installer_toutes_les_maj
                                        show_name: true
                                        show_icon: true
                                        show_state: false
                                        name: TOUT METTRE À JOUR
                                        icon: mdi:update
                                        tap_action:
                                          action: perform-action
                                          perform_action: script.turn_on
                                          target:
                                            entity_id: script.installer_toutes_les_maj
                                          confirmation:
                                            text: >-
                                              Voulez-vous lancer TOUTES les mises à
                                              jour disponibles ?
                                        styles:
                                          card:
                                            - height: 40px
                                            - margin: 0 0 10px 0
                                            - background: rgba(255,193,7,0.08)
                                            - border: 1px solid rgba(255,193,7,0.4)
                                            - border-radius: 6px
                                            - box-shadow: >-
                                                0 0 12px rgba(255,193,7,0.12), inset 0
                                                -2px 0 rgba(255,193,7,0.5)
                                          grid:
                                            - grid-template-areas: '"i n"'
                                            - grid-template-columns: 20px auto
                                            - grid-template-rows: 1fr
                                            - column-gap: 8px
                                            - justify-content: center
                                            - align-items: center
                                          icon:
                                            - width: 16px
                                            - color: '#ffc107'
                                            - filter: drop-shadow(0 0 4px
                                          name:
                                            - font-family: Orbitron, sans-serif
                                            - font-size: 11px
                                            - font-weight: 700
                                            - letter-spacing: 1.5px
                                            - color: '#ffc107'
                                            - text-shadow: 0 0 6px rgba(255,193,7,0.6)
                                            - justify-self: start
                                      - type: custom:auto-entities
                                        filter:
                                          include:
                                            - domain: update
                                              state: 'on'
                                        card:
                                          type: entities
                                          show_header_toggle: false
                        b2:
                          card:
                            type: custom:button-card
                            entity: update.hacs_update
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: |
                                    [[[
                                      let st = states['update.hacs_update']?.state;
                                      let hasUpdate = st === 'on';
                                      let hex = hasUpdate ? '#ffc107' : 'rgba(0,10,22,0.85)';
                                      return hasUpdate ? hex + '1c' : hex;
                                    ]]]
                                - border: |
                                    [[[
                                      let st = states['update.hacs_update']?.state;
                                      let hasUpdate = st === 'on';
                                      return hasUpdate ? '1px solid #ffc10772' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      let st = states['update.hacs_update']?.state;
                                      let hasUpdate = st === 'on';
                                      return hasUpdate ? '2px 4px 14px #ffc1072d, inset 0 -3px 0 #ffc107' : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      let st = states['update.hacs_update']?.state;
                                      return st === 'on' ? '52px' : '42px';
                                    ]]]
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  let st = states['update.hacs_update']?.state;
                                  let hasUpdate = st === 'on';
                                  if (!hasUpdate) {
                                    return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;">
                                      <ha-icon icon="mdi:store-24-hour" style="width:24px;height:24px;color:rgba(0,229,255,0.35);filter:none;"></ha-icon>
                                    </div>`;
                                  }
                                  let color = '#ffc107';
                                  return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;">
                                    <ha-icon icon="mdi:store-24-hour" style="width:24px;height:24px;color:${color};filter:drop-shadow(0 0 6px ${color});"></ha-icon>
                                  </div>`;
                                ]]]
                            tap_action:
                              action: navigate
                              navigation_path: /hacs
                        b3:
                          card:
                            type: custom:button-card
                            entity: light.prise_tv_switch
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: |
                                    [[[
                                      let st = states['light.prise_tv_switch']?.state;
                                      let ok = st === 'on' || st === 'off';
                                      let hex = ok ? '#32cd32' : '#ff1744';
                                      return hex + '1c';
                                    ]]]
                                - border: |
                                    [[[
                                      let st = states['light.prise_tv_switch']?.state;
                                      let ok = st === 'on' || st === 'off';
                                      let hex = ok ? '#32cd32' : '#ff1744';
                                      return '1px solid ' + hex + '72';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      let st = states['light.prise_tv_switch']?.state;
                                      let ok = st === 'on' || st === 'off';
                                      let hex = ok ? '#32cd32' : '#ff1744';
                                      return '2px 4px 14px ' + hex + '2d, inset 0 -3px 0 ' + hex;
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: 52px
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  let st = states['light.prise_tv_switch']?.state;
                                  let ok = st === 'on' || st === 'off';
                                  let color = ok ? 'limegreen' : '#ff1744';
                                  let anim = (!ok) ? 'animation: blink 2s ease infinite;' : '';
                                  return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;${anim}">
                                    <ha-icon icon="mdi:zigbee" style="width:24px;height:24px;color:${color};filter:drop-shadow(0 0 6px ${color});"></ha-icon>
                                  </div>`;
                                ]]]
                            tap_action:
                              action: perform-action
                              perform_action: python_script.reload_zha
                              confirmation:
                                text: Confirmer la relance de Zigbee HA ?
                        b4:
                          card:
                            type: custom:button-card
                            entity: binary_sensor.zigbee2mqtt_bridge_connection_state
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: |
                                    [[[
                                      let st = states['binary_sensor.zigbee2mqtt_bridge_connection_state']?.state;
                                      let ok = st === 'on';
                                      let hex = ok ? '#32cd32' : '#ff1744';
                                      return hex + '1c';
                                    ]]]
                                - border: |
                                    [[[
                                      let st = states['binary_sensor.zigbee2mqtt_bridge_connection_state']?.state;
                                      let ok = st === 'on';
                                      let hex = ok ? '#32cd32' : '#ff1744';
                                      return '1px solid ' + hex + '72';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      let st = states['binary_sensor.zigbee2mqtt_bridge_connection_state']?.state;
                                      let ok = st === 'on';
                                      let hex = ok ? '#32cd32' : '#ff1744';
                                      return '2px 4px 14px ' + hex + '2d, inset 0 -3px 0 ' + hex;
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: 52px
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  let st = states['binary_sensor.zigbee2mqtt_bridge_connection_state']?.state;
                                  let ok = st === 'on';
                                  let color = ok ? 'limegreen' : '#ff1744';
                                  let anim = (!ok) ? 'animation: blink 2s ease infinite;' : '';
                                  return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;${anim}">
                                    <ha-icon icon="phu:zigbee2mqtt" style="width:24px;height:24px;color:${color};filter:drop-shadow(0 0 6px ${color});"></ha-icon>
                                  </div>`;
                                ]]]
                            tap_action:
                              action: navigate
                              navigation_path: /45df7312_zigbee2mqtt
                        b5:
                          card:
                            type: custom:button-card
                            entity: sensor.nombre_de_piles_faibles
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: |
                                    [[[
                                      let n = parseInt(states['sensor.nombre_de_piles_faibles']?.state);
                                      let hasBattery = !isNaN(n) && n > 0;
                                      let hex = hasBattery ? '#ff1744' : 'rgba(0,10,22,0.85)';
                                      return hasBattery ? hex + '1c' : hex;
                                    ]]]
                                - border: |
                                    [[[
                                      let n = parseInt(states['sensor.nombre_de_piles_faibles']?.state);
                                      let hasBattery = !isNaN(n) && n > 0;
                                      return hasBattery ? '1px solid #ff174472' : '1px solid rgba(0,229,255,0.13)';
                                    ]]]
                                - border-top: none
                                - border-left: none
                                - box-shadow: |
                                    [[[
                                      let n = parseInt(states['sensor.nombre_de_piles_faibles']?.state);
                                      let hasBattery = !isNaN(n) && n > 0;
                                      return hasBattery ? '2px 4px 14px #ff17442d, inset 0 -3px 0 #ff1744' : 'none';
                                    ]]]
                                - border-radius: 0 0 8px 0
                                - height: |
                                    [[[
                                      let n = parseInt(states['sensor.nombre_de_piles_faibles']?.state);
                                      let hasBattery = !isNaN(n) && n > 0;
                                      return hasBattery ? '52px' : '42px';
                                    ]]]
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  let n = parseInt(states['sensor.nombre_de_piles_faibles']?.state);
                                  let hasBattery = !isNaN(n) && n > 0;
                                  if (!hasBattery) {
                                    return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;">
                                      <ha-icon icon="mdi:battery-alert" style="width:20px;height:20px;color:rgba(0,229,255,0.35);filter:none;"></ha-icon>
                                    </div>`;
                                  }
                                  let color = '#ff1744';
                                  let anim = 'animation: blink 2s ease infinite;';
                                  return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;gap:4px;${anim}">
                                    <ha-icon icon="mdi:battery-alert" style="width:20px;height:20px;color:${color};filter:drop-shadow(0 0 6px ${color});"></ha-icon>
                                    <span style="font-family:Orbitron,sans-serif;font-size:14px;font-weight:700;color:${color};text-shadow:0 0 8px ${color};">${n}</span>
                                  </div>`;
                                ]]]
                            tap_action:
                              action: fire-dom-event
                              browser_mod:
                                service: browser_mod.popup
                                data:
                                  title: GESTION DES PILES
                                  style: >
                                    --popup-background: rgba(0, 5, 15, 0.96);
                                    --popup-border-radius: 6px;
                                    --popup-border-color: rgba(0,229,255,0.22);
                                    --popup-box-shadow: 0 0 40px
                                    rgba(0,229,255,0.08); --ha-card-background:
                                    transparent; --mdc-theme-surface: rgba(0, 5,
                                    15, 0.96); --primary-text-color: #00e5ff;
                                    --secondary-text-color:
                                    rgba(0,229,255,0.55); --mdc-theme-primary:
                                    #00e5ff; backdrop-filter: blur(12px);
                                  content:
                                    type: custom:auto-entities
                                    card:
                                      type: entities
                                      show_header_toggle: false
                                    filter:
                                      template: |-
                                        {% for state in states.sensor -%}
                                          {%- if state.entity_id is match('sensor.*_battery_plus') -%}
                                            {%- set base_id = state.entity_id.replace('sensor.', '').replace('_battery_plus', '') -%}
                                            {%- set button_id = 'button.' ~ base_id ~ '_battery_replaced' -%}
                                            {{
                                              {
                                                'entity': state.entity_id,
                                                'type': 'custom:multiple-entity-row',
                                                'name': state.attributes.friendly_name | replace(' Battery plus', ''),
                                                'entities': [
                                                  {'attribute': 'battery_type', 'name': 'Modèle'},
                                                  {'entity': button_id, 'name': false, 'icon': 'mdi:sync'}
                                                ]
                                              }
                                            }},
                                          {%- endif -%}
                                        {%- endfor %}
                                    sort:
                                      method: state
                                      numeric: true
                                      reverse: false
                        b6:
                          card:
                            type: custom:button-card
                            icon: mdi:power
                            show_name: false
                            show_state: false
                            show_icon: false
                            styles:
                              card:
                                - background: rgba(255,23,68,0.12)
                                - border: 1px solid rgba(255,23,68,0.45)
                                - border-top: none
                                - border-left: none
                                - box-shadow: >-
                                    2px 4px 14px rgba(255,23,68,0.18), inset 0
                                    -3px 0 rgba(255,23,68,0.9)
                                - border-radius: 0 0 8px 0
                                - height: 52px
                              grid:
                                - grid-template-areas: '"val"'
                                - grid-template-columns: 1fr
                                - grid-template-rows: 1fr
                              custom_fields:
                                val:
                                  - align-self: center
                                  - justify-self: center
                                  - width: 100%
                                  - height: 100%
                            custom_fields:
                              val: |
                                [[[
                                  let n = parseInt(states['sensor.nombre_de_mises_a_jour']?.state);
                                  let hasUpdate = !isNaN(n) && n > 0;
                                  let anim = hasUpdate ? 'animation: blink 2s ease infinite;' : '';
                                  let color = '#ff1744';
                                  return `<div style="display:flex;align-items:center;justify-content:center;height:100%;width:100%;${anim}">
                                    <ha-icon icon="mdi:power" style="width:24px;height:24px;color:${color};filter:drop-shadow(0 0 6px ${color});"></ha-icon>
                                  </div>`;
                                ]]]
                            tap_action:
                              action: none
                            hold_action:
                              action: perform-action
                              perform_action: homeassistant.restart
                              confirmation:
                                text: Redémarrer Home Assistant ?
                  DecoLines: |
                    [[[
                      const isHome = states['device_tracker.homeassistant']?.state === 'home';
                      const ok = entity.state === 'on' || entity.state === 'off';
                      const c = ok ? (isHome ? 'limegreen' : '#00e5ff') : '#ffc107';
                      const cOff = ok ? (isHome ? 'rgba(50,205,50,0.18)' : 'rgba(0,229,255,0.18)') : 'rgba(255,193,7,0.18)';
                      const divLine = ok ? (isHome ? 'rgba(50,205,50,0.15)' : 'rgba(0,229,255,0.12)') : 'rgba(255,193,7,0.15)';
                      const topLine = `linear-gradient(90deg,transparent,${c} 30%,${c} 70%,transparent)`;
                      return `
                        <div style="position:absolute;top:0;left:0;right:0;height:1px;background:${topLine};opacity:0.65;pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;bottom:68px;left:0;right:0;height:1px;background:linear-gradient(90deg,transparent,${divLine},transparent);pointer-events:none;z-index:2;"></div>
                        <div style="position:absolute;top:-1px;left:-1px;width:12px;height:12px;border-top:2px solid ${c};border-left:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;top:-1px;right:-1px;width:12px;height:12px;border-top:2px solid ${c};border-right:2px solid ${c};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;left:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-left:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                        <div style="position:absolute;bottom:-1px;right:-1px;width:12px;height:12px;border-bottom:2px solid ${cOff};border-right:2px solid ${cOff};pointer-events:none;z-index:3;"></div>
                      `;
                    ]]]
            columns: 1
            grid_options:
              columns: full
    type: sections
    background:
      opacity: 10
      alignment: center
      size: cover
      repeat: no-repeat
      attachment: scroll
      image:
        media_content_id: media-source://image_upload/898a0bbded6e734dffb8957d5ad3dfa6
        media_content_type: image/jpeg
        metadata:
          title: 8036.jpg
          thumbnail: /api/image/serve/898a0bbded6e734dffb8957d5ad3dfa6/256x256
          media_class: image
          navigateIds:
            - {}
            - media_content_type: app
              media_content_id: media-source://image_upload
    cards: []
    path: affichage-tablette
    max_columns: 3
    dense_section_placement: false
    badges:
      - type: entity
        show_name: false
        show_state: true
        show_icon: true
        color: yellow
        entity: input_number.boite_aux_lettres_nombre_de_mouvement
        name: Courrier
        show_entity_picture: false
        visibility:
          - condition: numeric_state
            entity: input_number.boite_aux_lettres_nombre_de_mouvement
            above: 0
        tap_action:
          action: perform-action
          perform_action: input_number.set_value
          target:
            entity_id: input_number.boite_aux_lettres_nombre_de_mouvement
          data:
            value: 0
      - type: entity
        show_name: true
        show_state: false
        show_icon: true
        color: ''
        entity: media_player.echo_chambre
        show_entity_picture: true
        name: Ch. Parents
        visibility:
          - condition: state
            entity: media_player.echo_chambre
            state: playing
      - type: entity
        show_name: true
        show_state: false
        show_icon: true
        color: ''
        entity: media_player.echo_timeo
        show_entity_picture: true
        visibility:
          - condition: state
            entity: media_player.echo_timeo
            state: playing
        name: 'Timéo '
      - type: entity
        show_name: true
        show_state: false
        show_icon: true
        color: ''
        entity: media_player.menz_echo_dot
        show_entity_picture: true
        visibility:
          - condition: state
            entity: media_player.menz_echo_dot
            state: playing
        name: Chris
      - type: entity
        show_name: true
        show_state: false
        show_icon: true
        color: ''
        entity: media_player.echo_salle_de_bains
        show_entity_picture: true
        visibility:
          - condition: state
            entity: media_player.echo_salle_de_bains
            state: playing
        name: Salle de B.
      - type: entity
        show_name: true
        show_state: false
        show_icon: true
        color: ''
        entity: media_player.echo
        show_entity_picture: true
        visibility:
          - condition: state
            entity: media_player.echo
            state: playing
        name: Echo
      - type: entity
        show_name: true
        show_state: false
        show_icon: true
        color: red
        entity: sensor.jarvis_rinse_aid_nearly_empty
        name: Remplir Liquide Rinçage
        show_entity_picture: true
        tap_action:
          action: none
        visibility:
          - condition: state
            entity: sensor.jarvis_rinse_aid_nearly_empty
            state: present
      - type: entity
        show_name: true
        show_state: false
        show_icon: true
        color: red
        entity: sensor.jarvis_salt_nearly_empty
        name: Remplir Sel
        show_entity_picture: true
        tap_action:
          action: none
        visibility:
          - condition: state
            entity: sensor.jarvis_salt_nearly_empty
            state: present
      - type: entity
        show_name: true
        show_state: true
        show_icon: true
        color: red
        entity: binary_sensor.ouvertures
        name: Au moins une porte/fenêtre est
        icon: phu:patio-doors-open
        tap_action:
          action: none
        visibility:
          - condition: state
            entity: binary_sensor.ouvertures
            state: 'on'
      - type: entity
        show_name: false
        show_state: false
        show_icon: true
        color: accent
        entity: light.chauffe_eau
        tap_action:
          action: none
        visibility:
          - condition: state
            entity: light.chauffe_eau
            state: 'on'
        show_entity_picture: false
        icon: mdi:water-boiler
      - type: entity
        show_name: false
        show_state: false
        show_icon: true
        color: orange
        entity: sensor.etat_de_l_alame
        name: Désarmé
        icon: mdi:shield-half-full
        tap_action:
          action: none
        visibility:
          - condition: state
            entity: sensor.etat_de_l_alame
            state: ('unknown',)
      - type: entity
        show_name: false
        show_state: false
        show_icon: true
        color: red
        entity: sensor.etat_de_l_alame
        name: Désarmé
        icon: mdi:shield-lock
        tap_action:
          action: none
        visibility:
          - condition: state
            entity: sensor.etat_de_l_alame
            state: ('armed',)
      - type: entity
        show_name: true
        show_state: false
        show_icon: true
        color: accent
        entity: input_boolean.chauffage_mode_vacances
        icon: mdi:home-account
        show_entity_picture: false
        visibility:
          - condition: state
            entity: input_boolean.chauffage_mode_vacances
            state: 'on'
        state_content: name
        name: Vacances
        tap_action:
          action: perform-action
          perform_action: input_boolean.turn_off
          target:
            entity_id: input_boolean.chauffage_mode_vacances
      - type: entity
        show_name: true
        show_state: false
        show_icon: true
        entity: input_boolean.mode_absence
        color: accent
        name: Long
        visibility:
          - condition: state
            entity: input_boolean.mode_absence
            state: 'on'
        tap_action:
          action: perform-action
          perform_action: input_boolean.turn_off
          target:
            entity_id: input_boolean.mode_absence
      - type: entity
        show_name: true
        show_state: false
        show_icon: true
        color: accent
        entity: input_boolean.mode_absence_courte_duree
        name: Court
        icon: mdi:home-export-outline
        tap_action:
          action: perform-action
          perform_action: input_boolean.turn_off
          target:
            entity_id: input_boolean.mode_absence_courte_duree
        visibility:
          - condition: state
            entity: input_boolean.mode_absence_courte_duree
            state: 'on'
      - type: entity
        show_name: true
        show_state: false
        show_icon: true
        color: red
        icon: mdi:fireplace
        show_entity_picture: true
        tap_action:
          action: perform-action
          perform_action: input_boolean.turn_off
          target:
            entity_id: input_boolean.nettoyage_poele_brasier
          data: {}
        visibility:
          - condition: state
            entity: input_boolean.nettoyage_poele_brasier
            state: 'on'
          - condition: state
            entity: calendar.chauffage
            state: 'off'
        entity: input_boolean.nettoyage_poele_brasier
      - type: entity
        show_name: true
        show_state: false
        show_icon: true
        color: red
        icon: mdi:fireplace
        show_entity_picture: true
        tap_action:
          action: perform-action
          perform_action: input_boolean.turn_off
          target:
            entity_id:
              - input_boolean.nettoyage_poele_complet
          data: {}
        visibility:
          - condition: state
            entity: input_boolean.nettoyage_poele_complet
            state: 'on'
          - condition: state
            entity: calendar.chauffage
            state: 'off'
        entity: input_boolean.nettoyage_poele_complet
      - type: entity
        show_name: true
        show_state: true
        show_icon: true
        entity: sensor.roidmi_v60_f2ec_brush_life_level_3
        visibility:
          - condition: numeric_state
            entity: sensor.roidmi_v60_f2ec_brush_life_level_3
            below: 15
        name: Nettoyer Capteur
        tap_action:
          action: perform-action
          perform_action: ''
          target: {}
      - type: entity
        show_name: true
        show_state: false
        show_icon: true
        color: red
        icon: mdi:fireplace
        show_entity_picture: true
        tap_action:
          action: perform-action
          perform_action: input_boolean.turn_off
          target:
            entity_id: input_boolean.nettoyage_poele_vitre
          data: {}
        visibility:
          - condition: state
            entity: input_boolean.nettoyage_poele_vitre
            state: 'on'
          - condition: state
            entity: calendar.chauffage
            state: 'off'
        entity: input_boolean.nettoyage_poele_vitre
      - type: custom:entity-progress-badge
        entity: sensor.unknown_batterie
        min_value: 0
        max_value: 100
        name: Tablette HA
        theme: optimal_when_high
        hold_action:
          action: none
        double_tap_action:
          action: none
        tap_action:
          action: none
      - type: entity
        show_name: false
        show_state: true
        show_icon: true
        entity: sensor.statut_bb_8
        visibility:
          - condition: state
            state: En tonte
        color: green
        show_entity_picture: false
        tap_action:
          action: none
    theme: amoled
    top_margin: false
    header:
      layout: center
      badges_position: top
      badges_wrap: wrap
