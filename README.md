<!-- anashost_support_badges_start -->
[![Revolut.Me][revolut_me_shield]][revolut_me]
[![PayPal.Me][paypal_me_shield]][paypal_me]
[![ko_fi][ko_fi_shield]][ko_fi_me]
[![buymecoffee][buy_me_coffee_shield]][buy_me_coffee_me]
[![patreon][patreon_shield]][patreon_me]
<!-- anashost_support_badges_end -->
<!-- 
```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
-->

# Home Assistant Animated cards

Take your Home Assistant dashboard to the next level with dynamic, customizable animated cards that bring your smart home dashboard to life.

This [YouTube Video](https://youtu.be/5vYz37AqSO4) explains how to do it.

<hr>

![ezgif-8e9c11636a22c613](https://github.com/user-attachments/assets/4f5a1af0-d89b-4ed9-9c8c-36e678045580)

### Note (about mushroom entity cards animations)
- In `State Mode:` when the main card entity is active, both icon get colored and animation start playing.
- In `Number Mode:` when the condition is met, the animations start playing, regardless of the state of the main card entity, so card icon will be still grayed out to indicate that the entity (like a plug) is still `Off`.

### Cards:

<details>
  <summary><strong>1 - smartplug</summary>

  ```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
   action: toggle
icon: mdi:power-plug
icon_color: green
name: Smart Plug
card_mod:
	  style:
	    mushroom-shape-icon$: |
	      .shape {
	        {# ========== USER CONFIG ========== #}
	        {# true = number mode, false = state mode #}
	        {% set use_number      = false %}
	
	        {# STATE MODE SETTINGS #}
	        {% set state_entity    = 'switch.plug_6_local' %}
	        {% set active_value    = 'on' %}
	
	        {# OPTIONAL: NUMBER MODE SETTINGS #}
	        {% set number_entity   = 'sensor.plug_power' %}
	        
	        {# '>' '<' '=' '>=' '<=' #}
	        {% set number_operator = '>' %}
	        
	        {% set threshold       = 0.0 %}
	        {# ========== END USER CONFIG ====== #}
	
	        {# ======== TRIGGER DECISION LOGIC ======== #}
	        {% if use_number %}
	          {% set num = states(number_entity) | float(0) %}
	          
	          {# Evaluate numeric rule #}
	          {% if number_operator == '>' %}
	            {% set trigger_active = (num > threshold) %}
	          {% elif number_operator == '<' %}
	            {% set trigger_active = (num < threshold) %}
	          {% elif number_operator == '=' %}
	            {% set trigger_active = (num == threshold) %}
	          {% elif number_operator == '>=' %}
	            {% set trigger_active = (num >= threshold) %}
	          {% elif number_operator == '<=' %}
	            {% set trigger_active = (num <= threshold) %}
	          {% else %}
	            {% set trigger_active = false %}
	          {% endif %}
	
	        {% else %}
	          {# State mode #}
	          {% if states(state_entity) == active_value %}
	            {% set trigger_active = true %}
	          {% else %}
	            {% set trigger_active = false %}
	          {% endif %}
	        {% endif %}
	        {# =========== END TRIGGER LOGIC =========== #}
	
	        {% if trigger_active %}
	          --shape-animation: ring-breathe 1.8s ease-out infinite;
	          opacity: 1;
	        {% else %}
	          --shape-animation: none;
	          opacity: 0.7;
	        {% endif %}
	      }
	
	      @keyframes ring-breathe {
	        0%   { box-shadow: 0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.9); }
	        70%  { box-shadow: 0 0 0 14px rgba(var(--rgb-{{ config.icon_color }}), 0.0); }
	        100% { box-shadow: 0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.0); }
	      }
	    .: |
	      mushroom-shape-icon {
	        --icon-size: 65px;
	        display: flex;
	        margin: -18px 0 10px -20px !important;
	        padding-right: 10px;
	      }
	      ha-card {
	        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
	      }

  ```
</details>

<details>
  <summary><strong>2 - Charger</summary>

  ```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
   action: toggle
icon: mdi:power-socket-au
icon_color: green
name: Charger
card_mod:
	  style:
	    mushroom-shape-icon$: |
	      .shape {
	        {# ========== USER CONFIG ========== #}
	        {# true = number mode, false = state mode #}
	        {% set use_number      = false %}
	
	        {# STATE MODE SETTINGS #}
	        {% set state_entity    = 'switch.plug_6_local' %}
	        {% set active_value    = 'on' %}
	
	        {# OPTIONAL: NUMBER MODE SETTINGS #}
	        {% set number_entity   = 'sensor.plug_power' %}
	        
	        {# '>' '<' '=' '>=' '<=' #}
	        {% set number_operator = '>' %}
	        
	        {% set threshold       = 0.0 %}
	        {# ========== END USER CONFIG ====== #}
	
	        {# ======== TRIGGER DECISION LOGIC ======== #}
	        {% if use_number %}
	          {% set num = states(number_entity) | float(0) %}
	
	          {% if number_operator == '>' %}
	            {% set trigger_active = (num > threshold) %}
	          {% elif number_operator == '<' %}
	            {% set trigger_active = (num < threshold) %}
	          {% elif number_operator == '=' %}
	            {% set trigger_active = (num == threshold) %}
	          {% elif number_operator == '>=' %}
	            {% set trigger_active = (num >= threshold) %}
	          {% elif number_operator == '<=' %}
	            {% set trigger_active = (num <= threshold) %}
	          {% else %}
	            {% set trigger_active = false %}
	          {% endif %}
	        {% else %}
	          {% set trigger_active = (states(state_entity) == active_value) %}
	        {% endif %}
	        {# =========== END TRIGGER LOGIC =========== #}
	
	        {% if trigger_active %}
	          {# adjust speed based on this plug's own power attribute #}
	          {% set p = state_attr(config.entity, 'current_power_w') | float(0) %}
	          {% set dur = 1.6 - (p / 800) %}
	          --shape-animation: ultra-plug-on {{ [0.4, dur] | max | round(2) }}s ease-in-out infinite;
	          --plug-glow-animation: ultra-plug-glow 1.8s ease-in-out infinite;
	          --plug-arcs-animation: ultra-plug-arcs 1.2s linear infinite;
	          opacity: 1;
	        {% else %}
	          --shape-animation: none;
	          --plug-glow-animation: none;
	          --plug-arcs-animation: none;
	          opacity: 0.7;
	        {% endif %}
	
	        transform-origin: 50% 50%;
	        position: relative;
	      }
	
	      .shape::before,
	      .shape::after {
	        content: '';
	        position: absolute;
	        inset: -4px;
	        border-radius: inherit;
	        pointer-events: none;
	      }
	
	      .shape::before {
	        animation: var(--plug-glow-animation);
	      }
	
	      .shape::after {
	        animation: var(--plug-arcs-animation);
	      }
	
	      @keyframes ultra-plug-on {
	        0%   { transform: scale(1); }
	        25%  { transform: scale(1.05) translateY(-1px); }
	        50%  { transform: scale(1.08) translateY(-2px); }
	        75%  { transform: scale(1.04) translateY(-1px); }
	        100% { transform: scale(1); }
	      }
	
	      @keyframes ultra-plug-glow {
	        0% {
	          box-shadow:
	            0 0 10px 3px rgba(var(--rgb-{{ config.icon_color }}), 0.6),
	            0 0 20px 8px rgba(var(--rgb-{{ config.icon_color }}), 0.3);
	        }
	        50% {
	          box-shadow:
	            0 0 18px 6px rgba(var(--rgb-{{ config.icon_color }}), 0.9),
	            0 0 32px 12px rgba(var(--rgb-{{ config.icon_color }}), 0.4);
	        }
	        100% {
	          box-shadow:
	            0 0 10px 3px rgba(var(--rgb-{{ config.icon_color }}), 0.6),
	            0 0 20px 8px rgba(var(--rgb-{{ config.icon_color }}), 0.3);
	        }
	      }
	
	      @keyframes ultra-plug-arcs {
	        0% {
	          box-shadow:
	            -10px -6px 0 -4px rgba(var(--rgb-{{ config.icon_color }}), 0.0),
	            12px 4px 0 -4px rgba(var(--rgb-{{ config.icon_color }}), 0.0);
	        }
	        25% {
	          box-shadow:
	            -10px -6px 0 -4px rgba(var(--rgb-{{ config.icon_color }}), 0.7),
	            12px 4px 0 -4px rgba(var(--rgb-{{ config.icon_color }}), 0.4);
	        }
	        50% {
	          box-shadow:
	            -6px 2px 0 -4px rgba(var(--rgb-{{ config.icon_color }}), 0.3),
	            10px -8px 0 -4px rgba(var(--rgb-{{ config.icon_color }}), 0.7);
	        }
	        75% {
	          box-shadow:
	            -12px 4px 0 -4px rgba(var(--rgb-{{ config.icon_color }}), 0.5),
	            8px 0 0 -4px rgba(var(--rgb-{{ config.icon_color }}), 0.2);
	        }
	        100% {
	          box-shadow:
	            -10px -6px 0 -4px rgba(var(--rgb-{{ config.icon_color }}), 0.0),
	            12px 4px 0 -4px rgba(var(--rgb-{{ config.icon_color }}), 0.0);
	        }
	      }
	    .: |
	      mushroom-shape-icon {
	        --icon-size: 65px;
	        display: flex;
	        margin: -18px 0 10px -20px !important;
	        padding-right: 10px;
	      }
	      ha-card {
	        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
	      }

  ```
</details>

<details>
  <summary><strong>3 - Fan</summary>

  ```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
   action: toggle
icon: mdi:fan
icon_color: blue
name: Fan
card_mod:
	  style:
	    mushroom-shape-icon$: |
	      .shape {
	        {# ========== USER CONFIG ========== #}
	        {# true = number mode, false = state mode #}
	        {% set use_number      = false %}
	
	        {# STATE MODE SETTINGS #}
	        {% set state_entity    = 'switch.plug_6_local' %}
	        {% set active_value    = 'on' %}
	
	        {# OPTIONAL: NUMBER MODE SETTINGS #}
	        {% set number_entity   = 'sensor.plug_power' %}
	        
	        {# '>' '<' '=' '>=' '<=' #}
	        {% set number_operator = '>' %}
	        
	        {% set threshold       = 0.0 %}
	        {# ========== END USER CONFIG ====== #}
	
	        {# ======== TRIGGER DECISION LOGIC ======== #}
	        {% if use_number %}
	          {% set num = states(number_entity) | float(0) %}
	          {% if number_operator == '>' %}
	            {% set trigger_active = (num > threshold) %}
	          {% elif number_operator == '<' %}
	            {% set trigger_active = (num < threshold) %}
	          {% elif number_operator == '=' %}
	            {% set trigger_active = (num == threshold) %}
	          {% elif number_operator == '>=' %}
	            {% set trigger_active = (num >= threshold) %}
	          {% elif number_operator == '<=' %}
	            {% set trigger_active = (num <= threshold) %}
	          {% else %}
	            {% set trigger_active = false %}
	          {% endif %}
	        {% else %}
	          {% set trigger_active = (states(state_entity) == active_value) %}
	        {% endif %}
	        {# =========== END TRIGGER LOGIC =========== #}
	
	        {% if trigger_active %}
	          {# Fan speed logic based on THIS card's entity #}
	          {% if is_state(config.entity, 'on') %}
	            --shape-animation: fan-turbine 0.5s linear infinite;
	            opacity: 1;
	          {% elif is_state(config.entity, 'medium') %}
	            --shape-animation: fan-turbine 0.8s linear infinite;
	            opacity: 1;
	          {% elif is_state(config.entity, 'low') %}
	            --shape-animation: fan-turbine 1.2s linear infinite;
	            opacity: 1;
	          {% else %}
	            --shape-animation: fan-rock 2.4s ease-in-out infinite;
	            opacity: 0.7;
	          {% endif %}
	        {% else %}
	          --shape-animation: none;
	          opacity: 0.7;
	        {% endif %}
	
	        transform-origin: 50% 50%;
	        position: relative;
	        box-shadow:
	          0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.6),
	          0 0 0 6px rgba(var(--rgb-{{ config.icon_color }}), 0.08),
	          0 0 18px 0 rgba(var(--rgb-{{ config.icon_color }}), 0.7);
	      }
	
	      @keyframes fan-turbine {
	        0% {
	          transform: rotate(0deg) scale(1);
	          filter: drop-shadow(0 0 3px rgba(var(--rgb-{{ config.icon_color }}), 0.9));
	        }
	        25% {
	          transform: rotate(90deg) scale(1.02);
	        }
	        50% {
	          transform: rotate(180deg) scale(1.04);
	          filter: drop-shadow(0 0 8px rgba(var(--rgb-{{ config.icon_color }}), 1));
	        }
	        75% {
	          transform: rotate(270deg) scale(1.02);
	        }
	        100% {
	          transform: rotate(360deg) scale(1);
	          filter: drop-shadow(0 0 3px rgba(var(--rgb-{{ config.icon_color }}), 0.9));
	        }
	      }
	
	      @keyframes fan-rock {
	        0%   { transform: rotate(-4deg); }
	        50%  { transform: rotate(4deg); }
	        100% { transform: rotate(-4deg); }
	      }
	    .: |
	      mushroom-shape-icon {
	        --icon-size: 65px;
	        display: flex;
	        margin: -18px 0 10px -20px !important;
	        padding-right: 10px;
	      }
	      ha-card {
	        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
	      }

  ```
</details>

<details>
  <summary><strong>4 - Fan</summary>

  ```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
   action: toggle
icon: mdi:fan
icon_color: cyan
name: Fan
card_mod:
	  style:
	    mushroom-shape-icon$: |
	      .shape {
	        {# ========== USER CONFIG ========== #}
	        {# true = number mode, false = state mode #}
	        {% set use_number      = false %}
	
	        {# STATE MODE SETTINGS #}
	        {% set state_entity    = 'switch.plug_6_local' %}
	        {% set active_value    = 'on' %}
	
	        {# OPTIONAL: NUMBER MODE SETTINGS #}
	        {% set number_entity   = 'sensor.plug_power' %}
	        
	        {# '>' '<' '=' '>=' '<=' #}
	        {% set number_operator = '>' %}
	        
	        {% set threshold       = 0.0 %}
	        {# ========== END USER CONFIG ====== #}
	
	        {# -------- TRIGGER DECISION LOGIC -------- #}
	        {% if use_number %}
	          {% set num = states(number_entity) | float(0) %}
	          {% if number_operator == '>' %}
	            {% set trigger_active = (num > threshold) %}
	          {% elif number_operator == '<' %}
	            {% set trigger_active = (num < threshold) %}
	          {% elif number_operator == '=' %}
	            {% set trigger_active = (num == threshold) %}
	          {% elif number_operator == '>=' %}
	            {% set trigger_active = (num >= threshold) %}
	          {% elif number_operator == '<=' %}
	            {% set trigger_active = (num <= threshold) %}
	          {% else %}
	            {% set trigger_active = false %}
	          {% endif %}
	        {% else %}
	          {% set sensor_entity = state_entity %}
	          {% if states(sensor_entity) == active_value %}
	            {% set trigger_active = true %}
	          {% else %}
	            {% set trigger_active = false %}
	          {% endif %}
	        {% endif %}
	        {# ------ END TRIGGER DECISION LOGIC ------ #}
	
	        {# Fan speed still based on THIS card's entity percentage #}
	        {% set speed = state_attr(config.entity, 'percentage') | int(0) %}
	
	        {% if trigger_active %}
	          {% if speed == 0 and is_state(config.entity, 'off') %}
	            --shape-animation: fan-ultra-idle 3s ease-in-out infinite;
	          {% else %}
	            {% set dur = 1.2 - (speed / 120) %}
	            --shape-animation: fan-ultra-spin {{ [0.25, dur] | max | round(2) }}s linear infinite;
	          {% endif %}
	          --fan-warp-animation: fan-warp 1.4s ease-in-out infinite;
	          --fan-stream-animation: fan-stream 1.6s ease-in-out infinite;
	          opacity: 1;
	        {% else %}
	          --shape-animation: none;
	          --fan-warp-animation: none;
	          --fan-stream-animation: none;
	          opacity: 0.7;
	        {% endif %}
	
	        transform-origin: 50% 50%;
	        position: relative;
	      }
	
	      .shape::before,
	      .shape::after {
	        content: '';
	        position: absolute;
	        inset: 0;
	        border-radius: inherit;
	        pointer-events: none;
	      }
	
	      .shape::before {
	        animation: var(--fan-warp-animation);
	      }
	
	      .shape::after {
	        mix-blend-mode: screen;
	        animation: var(--fan-stream-animation);
	      }
	
	      @keyframes fan-ultra-spin {
	        0% {
	          transform: rotate(0deg) scale(1);
	          filter: drop-shadow(0 0 3px rgba(var(--rgb-{{ config.icon_color }}), 0.8));
	        }
	        20% { transform: rotate(72deg) scale(1.03); }
	        40% { transform: rotate(144deg) scale(1.05); }
	        60% { transform: rotate(216deg) scale(1.03); }
	        80% { transform: rotate(288deg) scale(1.02); }
	        100% {
	          transform: rotate(360deg) scale(1);
	          filter: drop-shadow(0 0 5px rgba(var(--rgb-{{ config.icon_color }}), 1));
	        }
	      }
	
	      @keyframes fan-ultra-idle {
	        0%   { transform: rotate(-6deg); }
	        50%  { transform: rotate(6deg); }
	        100% { transform: rotate(-6deg); }
	      }
	
	      @keyframes fan-warp {
	        0% {
	          box-shadow:
	            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.7),
	            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.25);
	        }
	        40% {
	          box-shadow:
	            0 0 0 8px rgba(var(--rgb-{{ config.icon_color }}), 0.4),
	            0 0 24px 6px rgba(var(--rgb-{{ config.icon_color }}), 0.25);
	        }
	        100% {
	          box-shadow:
	            0 0 0 18px rgba(var(--rgb-{{ config.icon_color }}), 0.0),
	            0 0 40px 12px rgba(var(--rgb-{{ config.icon_color }}), 0.0);
	        }
	      }
	
	      @keyframes fan-stream {
	        0% {
	          box-shadow:
	            20px 0 16px -12px rgba(var(--rgb-{{ config.icon_color }}), 0),
	            -20px 0 16px -12px rgba(var(--rgb-{{ config.icon_color }}), 0);
	        }
	        50% {
	          box-shadow:
	            24px 0 20px -10px rgba(var(--rgb-{{ config.icon_color }}), 0.6),
	            -24px 0 20px -10px rgba(var(--rgb-{{ config.icon_color }}), 0.4);
	        }
	        100% {
	          box-shadow:
	            20px 0 16px -12px rgba(var(--rgb-{{ config.icon_color }}), 0),
	            -20px 0 16px -12px rgba(var(--rgb-{{ config.icon_color }}), 0);
	        }
	      }
	    .: |
	      mushroom-shape-icon {
	        --icon-size: 65px;
	        display: flex;
	        margin: -18px 0 10px -20px !important;
	        padding-right: 10px;
	      }
	      ha-card {
	        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
	      }

  ```
</details>

<details>
  <summary><strong>5 - Fan</summary>

  ```yaml
type: custom:mushroom-fan-card
entity: fan.living_room_fan
show_percentage_control: true
show_oscillate_control: true
show_direction_control: true
collapsible_controls: false
fill_container: false
icon_animation: false
icon_type: icon
card_mod:
	  style:
	    mushroom-shape-icon$: |
	      .shape {
	        {# ========= COLOR CONFIG ========= #}
	        {# change to 'blue', 'red', 'purple', etc. #}
	        {% set fan_color = 'green' %}
	        --fan-color-rgb: var(--rgb-{{ fan_color }});
	        {# ================================ #}
	
	        {# ========== USER CONFIG ========== #}
	        {% set use_number      = false %}
	
	        {# STATE MODE SETTINGS #}
	        {% set state_entity    = 'fan.living_room_fan' %}
	        {% set active_value    = 'on' %}
	
	        {# OPTIONAL: NUMBER MODE SETTINGS #}
	        {% set number_entity   = 'sensor.plug_power' %}
	        {% set number_operator = '>' %}
	        {% set threshold       = 0.0 %}
	        {# ========== END USER CONFIG ====== #}
	
	        {# -------- TRIGGER DECISION LOGIC -------- #}
	        {% if use_number %}
	          {% set num = states(number_entity) | float(0) %}
	          {% if number_operator == '>' %}
	            {% set trigger_active = (num > threshold) %}
	          {% elif number_operator == '<' %}
	            {% set trigger_active = (num < threshold) %}
	          {% elif number_operator == '=' %}
	            {% set trigger_active = (num == threshold) %}
	          {% elif number_operator == '>=' %}
	            {% set trigger_active = (num >= threshold) %}
	          {% elif number_operator == '<=' %}
	            {% set trigger_active = (num <= threshold) %}
	          {% else %}
	            {% set trigger_active = false %}
	          {% endif %}
	        {% else %}
	          {% set sensor_entity = state_entity %}
	          {% if states(sensor_entity) == active_value %}
	            {% set trigger_active = true %}
	          {% else %}
	            {% set trigger_active = false %}
	          {% endif %}
	        {% endif %}
	        {# ------ END TRIGGER DECISION LOGIC ------ #}
	
	        {# Fan speed still based on THIS card's entity percentage #}
	        {% set speed = state_attr(config.entity, 'percentage') | int(0) %}
	
	        {% if trigger_active %}
	          {% if speed == 0 and is_state(config.entity, 'off') %}
	            --shape-animation: fan-ultra-idle 3s ease-in-out infinite;
	          {% else %}
	            {% set dur = 1.2 - (speed / 120) %}
	            --shape-animation: fan-ultra-spin {{ [0.25, dur] | max | round(2) }}s linear infinite;
	          {% endif %}
	          --fan-warp-animation: fan-warp 1.4s ease-in-out infinite;
	          --fan-stream-animation: fan-stream 1.6s ease-in-out infinite;
	          opacity: 1;
	        {% else %}
	          --shape-animation: none;
	          --fan-warp-animation: none;
	          --fan-stream-animation: none;
	          opacity: 0.7;
	        {% endif %}
	
	        transform-origin: 50% 50%;
	        position: relative;
	      }
	
	      .shape::before,
	      .shape::after {
	        content: '';
	        position: absolute;
	        inset: 0;
	        border-radius: inherit;
	        pointer-events: none;
	      }
	
	      .shape::before {
	        animation: var(--fan-warp-animation);
	      }
	
	      .shape::after {
	        mix-blend-mode: screen;
	        animation: var(--fan-stream-animation);
	      }
	
	      @keyframes fan-ultra-spin {
	        0% {
	          transform: rotate(0deg) scale(1);
	          filter: drop-shadow(0 0 3px rgba(var(--fan-color-rgb), 0.8));
	        }
	        20% { transform: rotate(72deg) scale(1.03); }
	        40% { transform: rotate(144deg) scale(1.05); }
	        60% { transform: rotate(216deg) scale(1.03); }
	        80% { transform: rotate(288deg) scale(1.02); }
	        100% {
	          transform: rotate(360deg) scale(1);
	          filter: drop-shadow(0 0 5px rgba(var(--fan-color-rgb), 1));
	        }
	      }
	
	      @keyframes fan-ultra-idle {
	        0%   { transform: rotate(-6deg); }
	        50%  { transform: rotate(6deg); }
	        100% { transform: rotate(-6deg); }
	      }
	
	      @keyframes fan-warp {
	        0% {
	          box-shadow:
	            0 0 0 0 rgba(var(--fan-color-rgb), 0.7),
	            0 0 0 0 rgba(var(--fan-color-rgb), 0.25);
	        }
	        40% {
	          box-shadow:
	            0 0 0 8px rgba(var(--fan-color-rgb), 0.4),
	            0 0 24px 6px rgba(var(--fan-color-rgb), 0.25);
	        }
	        100% {
	          box-shadow:
	            0 0 0 18px rgba(var(--fan-color-rgb), 0.0),
	            0 0 40px 12px rgba(var(--fan-color-rgb), 0.0);
	        }
	      }
	
	      @keyframes fan-stream {
	        0% {
	          box-shadow:
	            20px 0 16px -12px rgba(var(--fan-color-rgb), 0),
	           -20px 0 16px -12px rgba(var(--fan-color-rgb), 0);
	        }
	        50% {
	          box-shadow:
	            24px 0 20px -10px rgba(var(--fan-color-rgb), 0.6),
	           -24px 0 20px -10px rgba(var(--fan-color-rgb), 0.4);
	        }
	        100% {
	          box-shadow:
	            20px 0 16px -12px rgba(var(--fan-color-rgb), 0),
	           -20px 0 16px -12px rgba(var(--fan-color-rgb), 0);
	        }
	      }
	    .: |
	      mushroom-shape-icon {
	        --icon-size: 65px;
	        display: flex;
	        margin: -18px 0 10px -20px !important;
	        padding-right: 10px;
	      }
	      ha-card {
	        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
	      }

  ```
</details>

<details>
  <summary><strong>6 - Lock</summary>

  ```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
   action: toggle
icon: mdi:lock
icon_color: red
name: Lock
card_mod:
	  style:
	    mushroom-shape-icon$: |
	      .shape {
	        {# ========== USER CONFIG ========== #}
	        {# true = number mode, false = state mode #}
	        {% set use_number      = false %}
	
	        {# STATE MODE SETTINGS #}
	        {% set state_entity    = 'switch.plug_6_local' %}
	        {% set active_value    = 'on' %}
	
	        {# OPTIONAL: NUMBER MODE SETTINGS #}
	        {% set number_entity   = 'sensor.plug_power' %}
	        
	        {# '>' '<' '=' '>=' '<=' #}
	        {% set number_operator = '>' %}
	        
	        {% set threshold       = 0.0 %}
	        {# ========== END USER CONFIG ====== #}
	
	        {# ---------- TRIGGER DECISION LOGIC ---------- #}
	        {% if use_number %}
	          {% set num = states(number_entity) | float(0) %}
	          {% if number_operator == '>' %}
	            {% set trigger_active = (num > threshold) %}
	          {% elif number_operator == '<' %}
	            {% set trigger_active = (num < threshold) %}
	          {% elif number_operator == '=' %}
	            {% set trigger_active = (num == threshold) %}
	          {% elif number_operator == '>=' %}
	            {% set trigger_active = (num >= threshold) %}
	          {% elif number_operator == '<=' %}
	            {% set trigger_active = (num <= threshold) %}
	          {% else %}
	            {% set trigger_active = false %}
	          {% endif %}
	        {% else %}
	          {% set trigger_active = (states(state_entity) == active_value) %}
	        {% endif %}
	        {# ---------- END TRIGGER LOGIC ---------- #}
	
	
	        {% if trigger_active %}
	          {% if is_state(config.entity, 'locked') %}
	            --shape-animation: lock-secure 2.3s ease-in-out infinite;
	          {% elif is_state(config.entity, 'unlocking') or is_state(config.entity, 'locking') %}
	            --shape-animation: lock-action 0.7s ease-in-out infinite;
	          {% else %}
	            --shape-animation: lock-open 1.6s ease-in-out infinite;
	          {% endif %}
	
	          opacity: 1;
	
	          /* HUGE glow system */
	          --glow-1: 0 0 25px 8px rgba(var(--rgb-{{ config.icon_color }}), 1);
	          --glow-2: 0 0 55px 18px rgba(var(--rgb-{{ config.icon_color }}), 0.7);
	          --glow-3: 0 0 95px 35px rgba(var(--rgb-{{ config.icon_color }}), 0.45);
	          --glow-anim: lock-ultraglow 2.2s ease-in-out infinite;
	
	        {% else %}
	          --shape-animation: none;
	          --glow-1: none;
	          --glow-2: none;
	          --glow-3: none;
	          --glow-anim: none;
	          opacity: 0.6;
	        {% endif %}
	
	        transform-origin: 50% 50%;
	        position: relative;
	      }
	
	      /* Glow layers */
	      .shape::before,
	      .shape::after {
	        content: '';
	        position: absolute;
	        inset: -8px;
	        border-radius: inherit;
	        pointer-events: none;
	      }
	
	      .shape::before {
	        box-shadow: var(--glow-1), var(--glow-2), var(--glow-3);
	        animation: var(--glow-anim);
	      }
	
	      .shape::after {
	        inset: -18px;
	        box-shadow: 0 0 120px 40px rgba(var(--rgb-{{ config.icon_color }}), 0.25);
	        opacity: 0.9;
	        animation: var(--glow-anim);
	      }
	
	      @keyframes lock-ultraglow {
	        0% {
	          opacity: 0.9;
	          filter: brightness(1);
	        }
	        50% {
	          opacity: 1;
	          filter: brightness(1.4);
	        }
	        100% {
	          opacity: 0.9;
	          filter: brightness(1);
	        }
	      }
	
	      @keyframes lock-secure {
	        0%   { transform: rotate(0deg) scale(1); }
	        15%  { transform: rotate(-12deg) scale(1.02); }
	        30%  { transform: rotate(2deg) scale(1.03); }
	        40%  { transform: rotate(0deg) scale(1); }
	        100% { transform: rotate(0deg) scale(1); }
	      }
	
	      @keyframes lock-action {
	        0%   { transform: rotate(-25deg) scale(0.96); }
	        50%  { transform: rotate(25deg) scale(1.04); }
	        100% { transform: rotate(-25deg) scale(0.96); }
	      }
	
	      @keyframes lock-open {
	        0%   { transform: rotate(10deg); }
	        50%  { transform: rotate(18deg); }
	        100% { transform: rotate(10deg); }
	      }
	    .: |
	      mushroom-shape-icon {
	        --icon-size: 65px;
	        display: flex;
	        margin: -18px 0 10px -20px !important;
	        padding-right: 20px;
	      }
	      ha-card {
	        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
	      }

  ```
</details>

<details>
<summary><strong>7 - Projector</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:projector
icon_color: accent
name: Projector
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}
          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: proj-ultra 1.7s ease-in-out infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.65;
        {% endif %}

        transform-origin: 30% 50%;
        position: relative;
      }

      .shape::before,
      .shape::after {
        content: '';
        position: absolute;
        inset: -6px;
        border-radius: inherit;
        pointer-events: none;
      }

      .shape::before {
        background:
          radial-gradient(circle at 20% 50%, rgba(255,255,255,0.6), transparent 55%);
        transform-origin: 20% 50%;
        {% if trigger_active %}
          opacity: 0.8;
          animation: proj-beam 1.7s ease-in-out infinite;
          filter: blur(1px);
        {% else %}
          opacity: 0;           /* hide beam completely */
          animation: none;
          filter: none;
        {% endif %}
      }

      .shape::after {
        {% if trigger_active %}
          animation: proj-focus 1.2s ease-in-out infinite;
        {% else %}
          animation: none;
          box-shadow: none;     /* kill leftover glow */
        {% endif %}
      }

      @keyframes proj-ultra {
        0%   { transform: translateX(0) scale(1); }
        25%  { transform: translateX(1px) scale(1.02); }
        50%  { transform: translateX(0) scale(1.03); }
        75%  { transform: translateX(-1px) scale(1.02); }
        100% { transform: translateX(0) scale(1); }
      }

      @keyframes proj-standby {
        0%   { transform: scale(1); }
        50%  { transform: scale(1.02); }
        100% { transform: scale(1); }
      }

      @keyframes proj-beam {
        0% {
          opacity: 0.7;
          filter: blur(1px);
          transform: scaleX(1) scaleY(1);
        }
        50% {
          opacity: 1;
          filter: blur(0.5px);
          transform: scaleX(1.15) scaleY(1.05);
        }
        100% {
          opacity: 0.7;
          filter: blur(1px);
          transform: scaleX(1) scaleY(1);
        }
      }

      @keyframes proj-focus {
        0% {
          box-shadow:
            0 0 10px 3px rgba(var(--rgb-{{ config.icon_color }}), 0.7);
        }
        50% {
          box-shadow:
            0 0 20px 8px rgba(var(--rgb-{{ config.icon_color }}), 1);
        }
        100% {
          box-shadow:
            0 0 10px 3px rgba(var(--rgb-{{ config.icon_color }}), 0.7);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 0 10px -20px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }


```
</details>

<details>
<summary><strong>8 - Doorbell</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:bell
icon_color: red
name: Doorbell
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}


        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}
          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {# default: simple equality state trigger #}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}


        {% if trigger_active %}
          --shape-animation: doorbell-ring 0.9s ease-out infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.8;
        {% endif %}

        transform-origin: 50% 50%;
      }

      @keyframes doorbell-ring {
        0% {
          transform: scale(1.05);
          box-shadow:
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 1),
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.8),
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.4);
        }
        30% {
          transform: scale(0.95);
          box-shadow:
            0 0 0 4px  rgba(var(--rgb-{{ config.icon_color }}), 0.9),
            0 0 0 10px rgba(var(--rgb-{{ config.icon_color }}), 0.7),
            0 0 0 18px rgba(var(--rgb-{{ config.icon_color }}), 0.4);
        }
        60% {
          transform: scale(1.02);
          box-shadow:
            0 0 0 10px rgba(var(--rgb-{{ config.icon_color }}), 0.0),
            0 0 0 18px rgba(var(--rgb-{{ config.icon_color }}), 0.2),
            0 0 0 26px rgba(var(--rgb-{{ config.icon_color }}), 0.0);
        }
        100% {
          transform: scale(1);
          box-shadow:
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.0),
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.0),
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.0);
        }
      }

      @keyframes doorbell-idle {
        0%   { transform: scale(1); }
        50%  { transform: scale(1.03); }
        100% { transform: scale(1); }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 0 10px -20px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>9 - Sprinkler</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:sprinkler-variant
icon_color: blue
name: Sprinkler
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}
          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: irrig-ultra 2s ease-in-out infinite;
          --irrig-heads-animation: irrig-heads 1.6s ease-out infinite;
          --irrig-fog-animation: irrig-fog 2s ease-in-out infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          --irrig-heads-animation: none;
          --irrig-fog-animation: none;
          opacity: 0.75;
        {% endif %}

        position: relative;
      }

      .shape::before,
      .shape::after {
        content: '';
        position: absolute;
        border-radius: inherit;
        inset: 0;
        pointer-events: none;
      }

      .shape::before {
        animation: var(--irrig-heads-animation);
      }

      .shape::after {
        animation: var(--irrig-fog-animation);
      }

      @keyframes irrig-ultra {
        0%   { transform: translateY(0) scale(1); }
        25%  { transform: translateY(-2px) scale(1.02); }
        50%  { transform: translateY(-4px) scale(1.03); }
        75%  { transform: translateY(-2px) scale(1.02); }
        100% { transform: translateY(0) scale(1); }
      }

      @keyframes irrig-idle {
        0%   { transform: translateY(1px); }
        50%  { transform: translateY(-1px); }
        100% { transform: translateY(1px); }
      }

      @keyframes irrig-heads {
        0% {
          box-shadow:
            -10px 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0),
            0 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0),
            10px 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0);
        }
        20% {
          box-shadow:
            -10px 4px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.9),
            0 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0),
            10px 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0);
        }
        40% {
          box-shadow:
            -10px -2px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.4),
            0 4px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.9),
            10px 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0);
        }
        60% {
          box-shadow:
            -10px -8px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.1),
            0 -2px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.4),
            10px 4px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.9);
        }
        80% {
          box-shadow:
            -10px -12px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.0),
            0 -8px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.1),
            10px -2px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.4);
        }
        100% {
          box-shadow:
            -10px 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0),
            0 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0),
            10px 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0);
        }
      }

      @keyframes irrig-fog {
        0% {
          filter: blur(0);
        }
        50% {
          filter: blur(0.7px);
          box-shadow: 0 -14px 18px -8px rgba(var(--rgb-{{ config.icon_color }}), 0.45);
        }
        100% {
          filter: blur(0);
          box-shadow: 0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 20pxx 0px -18px !important;
        padding-right: 20px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>9 - Sprinkler (icon in corner version)</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:sprinkler-variant
icon_color: blue
name: Sprinkler
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}
          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: irrig-ultra 2s ease-in-out infinite;
          --irrig-heads-animation: irrig-heads 1.6s ease-out infinite;
          --irrig-fog-animation: irrig-fog 2s ease-in-out infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          --irrig-heads-animation: none;
          --irrig-fog-animation: none;
          opacity: 0.75;
        {% endif %}

        position: relative;
      }

      .shape::before,
      .shape::after {
        content: '';
        position: absolute;
        border-radius: inherit;
        inset: 0;
        pointer-events: none;
      }

      .shape::before {
        animation: var(--irrig-heads-animation);
      }

      .shape::after {
        animation: var(--irrig-fog-animation);
      }

      @keyframes irrig-ultra {
        0%   { transform: translateY(0) scale(1); }
        25%  { transform: translateY(-2px) scale(1.02); }
        50%  { transform: translateY(-4px) scale(1.03); }
        75%  { transform: translateY(-2px) scale(1.02); }
        100% { transform: translateY(0) scale(1); }
      }

      @keyframes irrig-idle {
        0%   { transform: translateY(1px); }
        50%  { transform: translateY(-1px); }
        100% { transform: translateY(1px); }
      }

      @keyframes irrig-heads {
        0% {
          box-shadow:
            -10px 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0),
            0 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0),
            10px 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0);
        }
        20% {
          box-shadow:
            -10px 4px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.9),
            0 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0),
            10px 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0);
        }
        40% {
          box-shadow:
            -10px -2px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.4),
            0 4px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.9),
            10px 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0);
        }
        60% {
          box-shadow:
            -10px -8px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.1),
            0 -2px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.4),
            10px 4px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.9);
        }
        80% {
          box-shadow:
            -10px -12px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.0),
            0 -8px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.1),
            10px -2px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.4);
        }
        100% {
          box-shadow:
            -10px 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0),
            0 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0),
            10px 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0);
        }
      }

      @keyframes irrig-fog {
        0% {
          filter: blur(0);
        }
        50% {
          filter: blur(0.7px);
          box-shadow: 0 -14px 18px -8px rgba(var(--rgb-{{ config.icon_color }}), 0.45);
        }
        100% {
          filter: blur(0);
          box-shadow: 0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -17px 0 10px -17px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>10 - Heater</strong></summary>

```yaml
type: custom:mushroom-climate-card
entity: climate.heatpump
collapsible_controls: false
show_temperature_control: true
fill_container: false
hvac_modes:
  - auto
  - heat_cool
  - cool
  - heat
  - dry
  - fan_only
  - "off"
name: Heater
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========= FLAME COLOR CONFIG ========= #}
        {% set flame_color = 'deep-orange' %}   {# 'red', 'amber', 'orange', etc. #}
        --flame-color-rgb: var(--rgb-{{ flame_color }});
        {# ===================================== #}

        {# ========== USER CONFIG ========== #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'climate.heatpump' %}
        {% set active_value    = 'heat' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        {% set number_operator = '>' %}
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ----------- TRIGGER LOGIC ----------- #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}
          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# --------- END TRIGGER LOGIC --------- #}

        {% if trigger_active %}
          # --shape-animation: flame-core 1.4s infinite;
          --flame-layer1: flame-layers 1.8s infinite;
          --flame-layer2: ember-pulse 2.4s infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          --flame-layer1: none;
          --flame-layer2: none;
          opacity: 0.6;
        {% endif %}

        position: relative;
        transform-origin: 50% 75%;
      }

      /* Flame layers */
      .shape::before,
      .shape::after {
        content: "";
        position: absolute;
        inset: -8px;
        border-radius: inherit;
        pointer-events: none;
        filter: blur(3px);
      }

      .shape::before {
        animation: var(--flame-layer1);
      }

      .shape::after {
        animation: var(--flame-layer2);
      }

      /* Core flame motion: chaotic vertical flicker + heat ripple */
      @keyframes flame-core {
        0%   { transform: translateY(0)    scale(1);     filter: brightness(1.1) hue-rotate(0deg); }
        12%  { transform: translateY(-2px) scale(1.05);  filter: brightness(1.4) hue-rotate(-10deg); }
        25%  { transform: translateY(1px)  scale(0.98);  filter: brightness(0.9) hue-rotate(8deg); }
        40%  { transform: translateY(-3px) scale(1.07);  filter: brightness(1.5) hue-rotate(-18deg); }
        55%  { transform: translateY(0)    scale(1.02);  filter: brightness(1.2) hue-rotate(10deg); }
        70%  { transform: translateY(-1px) scale(1.04);  filter: brightness(1.35) hue-rotate(-6deg); }
        85%  { transform: translateY(2px)  scale(0.97);  filter: brightness(0.95) hue-rotate(6deg); }
        100% { transform: translateY(0)    scale(1);     filter: brightness(1.1) hue-rotate(0deg); }
      }

      /* Outer flame shape: shifting blurred glow */
      @keyframes flame-layers {
        0% {
          box-shadow:
            0 0 14px 8px rgba(var(--flame-color-rgb), 0.8),
            0 -12px 24px -6px rgba(255, 200, 0, 0.4);
        }
        33% {
          box-shadow:
            0 0 20px 10px rgba(var(--flame-color-rgb), 1),
            0 -16px 30px -8px rgba(255, 160, 0, 0.5);
        }
        66% {
          box-shadow:
            0 0 18px 9px rgba(var(--flame-color-rgb), 0.9),
            0 -8px 20px -6px rgba(255, 220, 0, 0.35);
        }
        100% {
          box-shadow:
            0 0 14px 8px rgba(var(--flame-color-rgb), 0.8),
            0 -12px 24px -6px rgba(255, 200, 0, 0.4);
        }
      }

      /* Ember pulse: slow breathing glow */
      @keyframes ember-pulse {
        0% {
          box-shadow:
            0 0 30px 10px rgba(var(--flame-color-rgb), 0.25),
            0 0 60px 20px rgba(255, 120, 0, 0.15);
        }
        50% {
          box-shadow:
            0 0 50px 18px rgba(var(--flame-color-rgb), 0.5),
            0 0 90px 35px rgba(255, 150, 0, 0.25);
        }
        100% {
          box-shadow:
            0 0 30px 10px rgba(var(--flame-color-rgb), 0.25),
            0 0 60px 20px rgba(255, 120, 0, 0.15);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -17px 0 20px -17px !important;
        padding-right: 20px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>11 - LED Strip</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:led-strip-variant
icon_color: blue
name: LED Strip
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}

          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}

        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: rgb-wave 2.2s linear infinite;
          opacity: 1;
        {% else %}
          --shape-animation: rgb-off 3s ease-in-out infinite;
          opacity: 0.5;
        {% endif %}

        transform-origin: 50% 50%;
      }

      @keyframes rgb-wave {
        0% {
          filter: hue-rotate(0deg) brightness(1.1);
          box-shadow:
            0 0 12px 3px rgba(var(--rgb-{{ config.icon_color }}), 0.9);
          transform: scale(1);
        }
        25% {
          filter: hue-rotate(90deg) brightness(1.3);
          box-shadow:
            0 0 16px 6px rgba(var(--rgb-{{ config.icon_color }}), 1);
          transform: scale(1.04);
        }
        50% {
          filter: hue-rotate(180deg) brightness(1.15);
          box-shadow:
            0 0 20px 8px rgba(var(--rgb-{{ config.icon_color }}), 0.9);
          transform: scale(1.02);
        }
        75% {
          filter: hue-rotate(270deg) brightness(1.3);
          box-shadow:
            0 0 16px 6px rgba(var(--rgb-{{ config.icon_color }}), 1);
          transform: scale(1.04);
        }
        100% {
          filter: hue-rotate(360deg) brightness(1.1);
          box-shadow:
            0 0 12px 3px rgba(var(--rgb-{{ config.icon_color }}), 0.9);
          transform: scale(1);
        }
      }

      @keyframes rgb-off {
        0%   { filter: brightness(0.7); }
        50%  { filter: brightness(0.9); }
        100% { filter: brightness(0.7); }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 0 10px -20px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>12 - Washing Machine</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:washing-machine
icon_color: blue
name: Washing Machine
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}
          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: wash-cycle 1s cubic-bezier(0.45, 0.05, 0.55, 0.95) infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.8;
        {% endif %}

        transform-origin: 50% 55%;
      }

      @keyframes wash-cycle {
        0%   { transform: translateY(0) rotate(-8deg) scale(1); box-shadow: 0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.5); }
        20%  { transform: translateY(-3px) rotate(8deg) scale(1.02); box-shadow: 0 0 8px 4px rgba(var(--rgb-{{ config.icon_color }}), 0.3); }
        40%  { transform: translateY(2px) rotate(-6deg) scale(1.01); }
        60%  { transform: translateY(-2px) rotate(6deg) scale(1.02); }
        80%  { transform: translateY(1px) rotate(-4deg) scale(1.01); }
        100% { transform: translateY(0) rotate(-8deg) scale(1); box-shadow: 0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.2); }
      }

      @keyframes wash-idle {
        0%   { transform: rotate(0deg); }
        50%  { transform: rotate(2deg); }
        100% { transform: rotate(0deg); }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -22px 0 10px -22px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>13 - Washing Machine</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:washing-machine
icon_color: blue
name: Washing Machine
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}
          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: washer-chaos 1.1s cubic-bezier(0.25, 0.1, 0.25, 1) infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.85;
        {% endif %}

        transform-origin: 50% 55%;
        box-shadow:
          inset 0 0 0 2px rgba(var(--rgb-{{ config.icon_color }}), 0.5),
          0 0 10px 0 rgba(var(--rgb-{{ config.icon_color }}), 0.6);
        position: relative;
      }

      @keyframes washer-chaos {
        0% {
          transform: rotate(0deg) scale(1);
          box-shadow:
            inset 0 0 0 2px rgba(var(--rgb-{{ config.icon_color }}), 0.7),
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.6);
        }
        15%  { transform: rotate(18deg) translate(1px, -1px) scale(1.02); }
        30%  { transform: rotate(60deg) translate(-2px, 2px) scale(0.98); }
        45%  { transform: rotate(120deg) translate(3px, -2px) scale(1.03); }
        60%  { transform: rotate(190deg) translate(-1px, 3px) scale(0.97); }
        75%  { transform: rotate(260deg) translate(1px, -3px) scale(1.04); }
        90%  { transform: rotate(330deg) translate(-2px, 1px) scale(1.01); }
        100% {
          transform: rotate(360deg) translate(0, 0) scale(1);
          box-shadow:
            inset 0 0 0 2px rgba(var(--rgb-{{ config.icon_color }}), 0.7),
            0 0 10px 6px rgba(var(--rgb-{{ config.icon_color }}), 0.0);
        }
      }

      @keyframes washer-idle {
        0%   { transform: rotate(-2deg); }
        50%  { transform: rotate(2deg); }
        100% { transform: rotate(-2deg); }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -22px 0 10px -22px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>14 - Dishwasher</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:dishwasher
icon_color: blue
name: Dishwasher
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}
          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: dishwasher-swash 1.5s ease-in-out infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.8;
        {% endif %}

        transform-origin: 50% 55%;
      }

      @keyframes dishwasher-swash {
        0% {
          transform: scale(1) rotate(0deg);
          box-shadow:
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.8),
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.3);
          filter: blur(0);
        }
        25% {
          transform: scale(1.03) rotate(10deg);
          box-shadow:
            0 0 10px 3px rgba(var(--rgb-{{ config.icon_color }}), 0.9),
            0 0 18px 8px rgba(var(--rgb-{{ config.icon_color }}), 0.3);
          filter: blur(0.4px);
        }
        50% {
          transform: scale(0.98) rotate(-15deg);
          box-shadow:
            0 0 14px 6px rgba(var(--rgb-{{ config.icon_color }}), 0.7),
            0 0 26px 12px rgba(var(--rgb-{{ config.icon_color }}), 0.2);
          filter: blur(0.6px);
        }
        75% {
          transform: scale(1.04) rotate(15deg);
          box-shadow:
            0 0 10px 3px rgba(var(--rgb-{{ config.icon_color }}), 0.9),
            0 0 18px 8px rgba(var(--rgb-{{ config.icon_color }}), 0.3);
          filter: blur(0.4px);
        }
        100% {
          transform: scale(1) rotate(0deg);
          box-shadow:
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.0),
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.0);
          filter: blur(0);
        }
      }

      @keyframes dishwasher-idle {
        0%   { transform: scale(1); }
        50%  { transform: scale(1.02); }
        100% { transform: scale(1); }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 0 10px -20px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>15 - Fireplace</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:fire
icon_color: deep-orange
name: Fireplace
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ----------- TRIGGER LOGIC ----------- #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}
          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# --------- END TRIGGER LOGIC --------- #}

        {% if trigger_active %}
          --shape-animation: flame-core 1.4s infinite;
          --flame-layer1: flame-layers 1.8s infinite;
          --flame-layer2: ember-pulse 2.4s infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          --flame-layer1: none;
          --flame-layer2: none;
          opacity: 0.6;
        {% endif %}

        position: relative;
        transform-origin: 50% 75%;
      }

      /* Flame layers */
      .shape::before,
      .shape::after {
        content: "";
        position: absolute;
        inset: -8px;
        border-radius: inherit;
        pointer-events: none;
        filter: blur(3px);
      }

      .shape::before {
        animation: var(--flame-layer1);
      }

      .shape::after {
        animation: var(--flame-layer2);
      }


      /* Core flame motion: chaotic vertical flicker + heat ripple */
      @keyframes flame-core {
        0%   { transform: translateY(0)    scale(1);     filter: brightness(1.1) hue-rotate(0deg); }
        12%  { transform: translateY(-2px) scale(1.05);  filter: brightness(1.4) hue-rotate(-10deg); }
        25%  { transform: translateY(1px)  scale(0.98);  filter: brightness(0.9) hue-rotate(8deg); }
        40%  { transform: translateY(-3px) scale(1.07);  filter: brightness(1.5) hue-rotate(-18deg); }
        55%  { transform: translateY(0)    scale(1.02);  filter: brightness(1.2) hue-rotate(10deg); }
        70%  { transform: translateY(-1px) scale(1.04);  filter: brightness(1.35) hue-rotate(-6deg); }
        85%  { transform: translateY(2px)  scale(0.97);  filter: brightness(0.95) hue-rotate(6deg); }
        100% { transform: translateY(0)    scale(1);     filter: brightness(1.1) hue-rotate(0deg); }
      }


      /* Outer flame shape: shifting blurred glow */
      @keyframes flame-layers {
        0% {
          box-shadow:
            0 0 14px 8px rgba(var(--rgb-{{ config.icon_color }}), 0.8),
            0 -12px 24px -6px rgba(255, 200, 0, 0.4);
        }
        33% {
          box-shadow:
            0 0 20px 10px rgba(var(--rgb-{{ config.icon_color }}), 1),
            0 -16px 30px -8px rgba(255, 160, 0, 0.5);
        }
        66% {
          box-shadow:
            0 0 18px 9px rgba(var(--rgb-{{ config.icon_color }}), 0.9),
            0 -8px 20px -6px rgba(255, 220, 0, 0.35);
        }
        100% {
          box-shadow:
            0 0 14px 8px rgba(var(--rgb-{{ config.icon_color }}), 0.8),
            0 -12px 24px -6px rgba(255, 200, 0, 0.4);
        }
      }


      /* Ember pulse: slow breathing glow */
      @keyframes ember-pulse {
        0% {
          box-shadow:
            0 0 30px 10px rgba(var(--rgb-{{ config.icon_color }}), 0.25),
            0 0 60px 20px rgba(255, 120, 0, 0.15);
        }
        50% {
          box-shadow:
            0 0 50px 18px rgba(var(--rgb-{{ config.icon_color }}), 0.5),
            0 0 90px 35px rgba(255, 150, 0, 0.25);
        }
        100% {
          box-shadow:
            0 0 30px 10px rgba(var(--rgb-{{ config.icon_color }}), 0.25),
            0 0 60px 20px rgba(255, 120, 0, 0.15);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 0 20px -20px !important;
        padding-right: 20px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>16 - Vacuum</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:robot-vacuum
icon_color: blue
name: Vacuum
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}

          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}

        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: robo-path 3.4s linear infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.7;
        {% endif %}

        transform-origin: 50% 50%;
      }

      @keyframes robo-path {
        0%   { transform: translate(0, 0) scale(1); }
        10%  { transform: translate(6px, -2px) rotate(8deg) scale(0.98); }
        20%  { transform: translate(10px, 4px) rotate(-6deg) scale(1); }
        30%  { transform: translate(4px, 8px) rotate(-14deg) scale(1.02); }
        40%  { transform: translate(-6px, 10px) rotate(4deg) scale(0.98); }
        50%  { transform: translate(-10px, 2px) rotate(16deg) scale(1.03); }
        60%  { transform: translate(-6px, -6px) rotate(-8deg) scale(1); }
        70%  { transform: translate(2px, -10px) rotate(10deg) scale(0.97); }
        80%  { transform: translate(8px, -6px) rotate(-6deg) scale(1.02); }
        90%  { transform: translate(4px, -2px) rotate(4deg) scale(1.01); }
        100% { transform: translate(0, 0) rotate(0deg) scale(1); }
      }

      @keyframes robo-dock {
        0%   { transform: translateY(0); }
        50%  { transform: translateY(-2px); }
        100% { transform: translateY(0); }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 20pxx 0px -18px !important;
        padding-right: 20px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>17 - 3d Printer</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:printer-3d-nozzle
icon_color: purple
name: 3d Printer
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}

          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}

        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: printer-sequence 2.3s linear infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.8;
        {% endif %}

        transform-origin: 50% 80%;
        box-shadow:
          0 8px 0 -4px rgba(var(--rgb-{{ config.icon_color }}), 0.5),
          0 0 12px 0 rgba(var(--rgb-{{ config.icon_color }}), 0.8);
      }

      @keyframes printer-sequence {
        0%   { transform: translate(-5px, 4px) scale(0.96); }
        15%  { transform: translate(5px, 4px) scale(0.96); }
        16%  { transform: translate(5px, 2px) scale(0.98); }
        30%  { transform: translate(-5px, 2px) scale(0.98); }
        31%  { transform: translate(-5px, 0px) scale(1); }
        45%  { transform: translate(5px, 0px) scale(1); }
        46%  { transform: translate(5px, -2px) scale(1.02); }
        60%  { transform: translate(-5px, -2px) scale(1.02); }
        61%  { transform: translate(-5px, -4px) scale(1.04); }
        80%  { transform: translate(5px, -4px) scale(1.04); }
        100% { transform: translate(-5px, 4px) scale(0.96); }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -22px 0 10px -22px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>18 - PIR Sensor</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:motion-sensor
icon_color: red
name: PIR Sensor
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}

          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: motion-active 1.4s linear infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.7;
        {% endif %}

        transform-origin: 50% 50%;
      }

      @keyframes motion-active {
        0% {
          transform: scale(1);
          box-shadow:
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.9),
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.4);
        }
        40% {
          transform: scale(1.06);
          box-shadow:
            0 0 0 6px rgba(var(--rgb-{{ config.icon_color }}), 0.5),
            0 0 0 12px rgba(var(--rgb-{{ config.icon_color }}), 0.2);
        }
        100% {
          transform: scale(1);
          box-shadow:
            0 0 0 16px rgba(var(--rgb-{{ config.icon_color }}), 0.0),
            0 0 0 24px rgba(var(--rgb-{{ config.icon_color }}), 0.0);
        }
      }

      @keyframes motion-scan {
        0% {
          transform: rotate(0deg);
          box-shadow:
            0 -10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.9);
        }
        50% {
          transform: rotate(180deg);
          box-shadow:
            0 10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.6);
        }
        100% {
          transform: rotate(360deg);
          box-shadow:
            0 -10px 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.9);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -15px 0 10px -18px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>19 - Alarm</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:alarm
icon_color: pink
name: Alarm
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}

          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: siren-alert 0.6s linear infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.7;
        {% endif %}

        transform-origin: 50% 50%;
      }

      @keyframes siren-alert {
        0% {
          transform: translate(0, 0) scale(1);
          box-shadow: 0 0 10px 4px rgba(var(--rgb-{{ config.icon_color }}), 1);
        }
        10%  { transform: translate(-2px, -1px) rotate(-3deg) scale(1.02); }
        20%  { transform: translate(3px, 1px) rotate(2deg) scale(1.03); }
        30%  { transform: translate(-4px, 0) rotate(-4deg) scale(1.04); }
        40%  { transform: translate(4px, 2px) rotate(3deg) scale(1.05); }
        50%  { transform: translate(-2px, -2px) rotate(-2deg) scale(1.02); }
        60%  { transform: translate(2px, 1px) rotate(1deg) scale(1.03); }
        70%  { transform: translate(-3px, 0) rotate(-3deg) scale(1.04); }
        80%  { transform: translate(3px, -1px) rotate(2deg) scale(1.03); }
        100% {
          transform: translate(0, 0) rotate(0deg) scale(1);
          box-shadow: 0 0 20px 8px rgba(var(--rgb-{{ config.icon_color }}), 0);
        }
      }

      @keyframes siren-armed {
        0%   { box-shadow: 0 0 4px 1px rgba(var(--rgb-{{ config.icon_color }}), 0.4); }
        50%  { box-shadow: 0 0 10px 4px rgba(var(--rgb-{{ config.icon_color }}), 0.9); }
        100% { box-shadow: 0 0 4px 1px rgba(var(--rgb-{{ config.icon_color }}), 0.4); }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 0 10px -20px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>20 - Heater</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
icon: mdi:radiator
icon_color: amber
name: Heater
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {% set power_val = states(number_entity) | float(0) %}
        {% set norm = (power_val / 1500) | float %}
        {% set norm_c = [0, [norm, 1.3] | min] | max %}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% if number_operator == '>' %}
            {% set trigger_active = (power_val > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (power_val < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (power_val == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (power_val >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (power_val <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: heater-waves 1.7s ease-in-out infinite;
          --heater-glow-animation: heater-glow 2.1s ease-in-out infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          --heater-glow-animation: none;
          opacity: 0.6;
        {% endif %}

        --heater-scale: {{ (1 + norm_c * 0.15) | round(3) }};
        transform-origin: 50% 60%;
        position: relative;
      }

      .shape::before,
      .shape::after {
        content: '';
        position: absolute;
        inset: -8px;
        border-radius: inherit;
        pointer-events: none;
      }

      .shape::before {
        animation: var(--heater-glow-animation);
      }

      .shape::after {
        inset: -18px;
        animation: var(--heater-glow-animation);
        opacity: 0.9;
      }

      @keyframes heater-waves {
        0%   { transform: translateY(0) scale(1); }
        25%  { transform: translateY(-1px) scale(var(--heater-scale)); }
        50%  { transform: translateY(-2px) scale(1.02); }
        75%  { transform: translateY(-1px) scale(var(--heater-scale)); }
        100% { transform: translateY(0) scale(1); }
      }

      @keyframes heater-glow {
        0% {
          box-shadow:
            0 -10px 18px -6px rgba(var(--rgb-{{ config.icon_color }}), 0.6),
            0 -20px 40px -10px rgba(255, 120, 0, 0.35);
        }
        50% {
          box-shadow:
            0 -14px 26px -4px rgba(var(--rgb-{{ config.icon_color }}), 1),
            0 -26px 60px -10px rgba(255, 90, 0, 0.55);
        }
        100% {
          box-shadow:
            0 -10px 18px -6px rgba(var(--rgb-{{ config.icon_color }}), 0.6),
            0 -20px 40px -10px rgba(255, 120, 0, 0.35);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -15px 0 10px -15px !important;
        padding-right: 20px;

      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>21 - RGB</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:led-on
icon_color: blue
name: RGB
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}

          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% if states(state_entity) == active_value %}
            {% set trigger_active = true %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: rainbow 3s linear infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.7;
        {% endif %}
      }

      @keyframes rainbow {
        0%   { filter: hue-rotate(0deg) brightness(1.1); }
        50%  { filter: hue-rotate(180deg) brightness(1.3); }
        100% { filter: hue-rotate(360deg) brightness(1.1); }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 0 10px -20px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>22 - Kettle</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:kettle-steam
icon_color: brown
name: Kettle
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}
          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: tv-glitch 1.2s linear infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.6;
        {% endif %}

        transform-origin: 50% 50%;
      }

      @keyframes tv-glitch {
        0%   { transform: scale(1) translate(0, 0); filter: brightness(1.05); box-shadow: 0 0 8px 2px rgba(var(--rgb-{{ config.icon_color }}), 0.7); }
        10%  { transform: scaleX(1.02) translate(-1px, 0); }
        20%  { transform: scaleX(0.98) translate(1px, -1px); filter: brightness(1.3); }
        25%  { transform: scale(1.01) translate(0, 1px); }
        40%  { transform: scale(1) translate(0, 0); filter: brightness(1.1); }
        55%  { transform: scaleX(1.03) translate(0, -1px); }
        70%  { transform: scale(1) translate(0, 0); box-shadow: 0 0 14px 5px rgba(var(--rgb-{{ config.icon_color }}), 0.9); }
        100% { transform: scale(1) translate(0, 0); filter: brightness(1.05); box-shadow: 0 0 8px 2px rgba(var(--rgb-{{ config.icon_color }}), 0.7); }
      }

      @keyframes tv-standby {
        0%   { box-shadow: 0 0 2px 0 rgba(var(--rgb-{{ config.icon_color }}), 0.3); }
        50%  { box-shadow: 0 0 6px 2px rgba(var(--rgb-{{ config.icon_color }}), 0.6); }
        100% { box-shadow: 0 0 2px 0 rgba(var(--rgb-{{ config.icon_color }}), 0.3); }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 0 10px -15px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>23 - Android TV</strong></summary>

```yaml
type: custom:mushroom-media-player-card
entity: media_player.android_tv
volume_controls:
  - volume_mute
  - volume_set
  - volume_buttons
media_controls:
  - on_off
  - shuffle
  - previous
  - play_pause_stop
  - next
  - repeat
show_volume_level: true
use_media_info: false
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ======= COLOR CONFIG ======= #}
        {% set icon_on_color      = 'red' %}
        {% set circle_on_rgb      = '255, 30, 30' %}     {# dark red #}
        {% set circle_on_opacity  = 0.1 %}

        {% set icon_off_color     = 'grey' %}
        {% set circle_off_rgb     = '80, 80, 80' %}      {# dark grey #}
        {% set circle_off_opacity = 0.25 %}
        {# ============================ #}

        {% set s = states('media_player.android_tv') %}

        {% if s != 'off' %}
          {% set trigger = true %}
        {% else %}
          {% set trigger = false %}
        {% endif %}

        {% if trigger %}
          /* icon color */
          --icon-color: rgb(var(--rgb-{{ icon_on_color }}));
          --icon-color-rgb: var(--rgb-{{ icon_on_color }});

          /* circle color */
          --shape-color: rgba({{ circle_on_rgb }}, {{ circle_on_opacity }});
          --shape-color-rgb: {{ circle_on_rgb }};

          --shape-animation: tv-rgb 1.4s linear infinite;
          --glow-enabled: 1;
          opacity: 1;
        {% else %}
          /* icon color */
          --icon-color: rgb(var(--rgb-{{ icon_off_color }}));
          --icon-color-rgb: var(--rgb-{{ icon_off_color }});

          /* circle color */
          --shape-color: rgba({{ circle_off_rgb }}, {{ circle_off_opacity }});
          --shape-color-rgb: {{ circle_off_rgb }};

          --shape-animation: none;
          --glow-enabled: 0;
          opacity: 0.7;
        {% endif %}

        transform-origin: 50% 50%;
      }

      /* Remove glow when off */
      .shape[style*="--glow-enabled: 0"] {
        filter: none !important;
        box-shadow: none !important;
      }
      .shape[style*="--glow-enabled: 0"]::before,
      .shape[style*="--glow-enabled: 0"]::after {
        animation: none !important;
        filter: none !important;
        box-shadow: none !important;
      }

      @keyframes tv-rgb {
        0% {
          transform: scale(1); /* no movement */
          filter: brightness(1.0);
          box-shadow:
            0 0 6px 2px rgba(var(--icon-color-rgb), 0.4),
            -4px 0 8px -4px rgba(255, 0, 0, 0.4),
             4px 0 8px -4px rgba(0, 128, 255, 0.4);
        }
        40% {
          transform: scale(1); /* still */
          filter: brightness(1.25);
          box-shadow:
            0 0 16px 6px rgba(var(--icon-color-rgb), 0.9),
            -6px 0 12px -4px rgba(255, 0, 0, 0.7),
             6px 0 12px -4px rgba(0, 128, 255, 0.7);
        }
        100% {
          transform: scale(1); /* still */
          filter: brightness(1.0);
          box-shadow:
            0 0 6px 2px rgba(var(--icon-color-rgb), 0.4),
            -4px 0 8px -4px rgba(255, 0, 0, 0.4),
             4px 0 8px -4px rgba(0, 128, 255, 0.4);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 0px 0px -15px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius,12px));
      }

```
</details>

<details>
<summary><strong>24 - Living Room Speaker</strong></summary>

```yaml
type: custom:mushroom-media-player-card
entity: media_player.living_room_speaker
volume_controls:
  - volume_mute
  - volume_set
  - volume_buttons
media_controls:
  - on_off
  - shuffle
  - previous
  - play_pause_stop
  - next
  - repeat
show_volume_level: true
use_media_info: false
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ====== COLOR CONFIG ====== #}
        {% set icon_on_color      = 'red' %}
        {% set circle_on_rgb      = '255, 30, 30' %}
        {% set circle_on_opacity  = 0.1 %}

        {% set icon_off_color     = 'grey' %}
        {% set circle_off_rgb     = '80, 80, 80' %}
        {% set circle_off_opacity = 0.25 %}
        {# ========================== #}

        {# ========== USER CONFIG ========== #}
        {# STATE ENTITY #}
        {% set state_entity    = 'media_player.living_room_speaker' %}
        {% set off_value       = 'off' %}
        {% set play_value      = 'playing' %}
        {% set idle_value      = 'idle' %}
        {# ========== END CONFIG ========== #}

        {% set st = states(state_entity) %}

        {# LIGHT ACTIVE = any state except off #}
        {% set light_active = (st != off_value) %}

        {# ANIMATION TRIGGER: default = only when playing #}
          {# animation ONLY when playing #}
          {% set trigger_active = (st == play_value) %}

        {% set vol = state_attr(config.entity, 'volume_level') | float(0) %}

        {% if light_active %}
          {# ---- COLORS WHEN NOT OFF ---- #}
          --icon-color: rgb(var(--rgb-{{ icon_on_color }}));
          --icon-color-rgb: var(--rgb-{{ icon_on_color }});
          --shape-color: rgba({{ circle_on_rgb }}, {{ circle_on_opacity }});
          --shape-color-rgb: {{ circle_on_rgb }};

          opacity: 1;

          {# ---- ANIMATION ONLY WHEN trigger_active ---- #}
          {% if trigger_active %}
            --shape-animation: ultra-speaker-main 0.9s ease-in-out infinite;
            --speaker-bars-animation: ultra-speaker-bars 0.55s linear infinite;
            --speaker-bass-animation: ultra-speaker-bass 1s ease-out infinite;
          {% else %}
            --shape-animation: none;
            --speaker-bars-animation: none;
            --speaker-bass-animation: none;
            --no-glow: 0 0 0 0 rgba(0,0,0,0);
          {% endif %}

        {% else %}
          {# ---- OFF: DIM + NO ANIMATION ---- #}
          --icon-color: rgb(var(--rgb-{{ icon_off_color }}));
          --icon-color-rgb: var(--rgb-{{ icon_off_color }});
          --shape-color: rgba({{ circle_off_rgb }}, {{ circle_off_opacity }});
          --shape-color-rgb: {{ circle_off_rgb }};

          --shape-animation: none;
          --speaker-bars-animation: none;
          --speaker-bass-animation: none;
          --no-glow: 0 0 0 0 rgba(0,0,0,0);

          opacity: 0.6;
        {% endif %}

        transform-origin: 50% 50%;
        position: relative;
      }

      .shape::before,
      .shape::after {
        content: '';
        position: absolute;
        inset: 0;
        border-radius: inherit;
        pointer-events: none;
      }

      .shape::before {
        animation: var(--speaker-bars-animation);
        box-shadow: var(--no-glow, none);
      }

      .shape::after {
        animation: var(--speaker-bass-animation);
        box-shadow: var(--no-glow, none);
      }

      @keyframes ultra-speaker-main {
        0%   { transform: scale(1); }
        50%  { transform: scale(1.06); }
        100% { transform: scale(1); }
      }

      @keyframes ultra-speaker-bars {
        0% {
          box-shadow:
            -10px 6px 0 -5px rgba(var(--icon-color-rgb), 0.4),
            0 2px 0 -5px rgba(var(--icon-color-rgb), 0.6),
            10px -4px 0 -5px rgba(var(--icon-color-rgb), 0.3);
        }
        33% {
          box-shadow:
            -10px -2px 0 -5px rgba(var(--icon-color-rgb), 0.7),
            0 8px 0 -5px rgba(var(--icon-color-rgb), 0.3),
            10px -8px 0 -5px rgba(var(--icon-color-rgb), 0.6);
        }
        66% {
          box-shadow:
            -10px -8px 0 -5px rgba(var(--icon-color-rgb), 0.3),
            0 -4px 0 -5px rgba(var(--icon-color-rgb), 0.7),
            10px 6px 0 -5px rgba(var(--icon-color-rgb), 0.5);
        }
        100% {
          box-shadow:
            -10px 6px 0 -5px rgba(var(--icon-color-rgb), 0.4),
            0 2px 0 -5px rgba(var(--icon-color-rgb), 0.6),
            10px -4px 0 -5px rgba(var(--icon-color-rgb), 0.3);
        }
      }

      @keyframes ultra-speaker-bass {
        0% {
          {% set s0 = 1 + (vol * 0.09) %}
          transform: scale({{ s0 | round(2) }});
          box-shadow:
            0 0 0 0 rgba(var(--icon-color-rgb), 0.9),
            0 0 0 0 rgba(var(--icon-color-rgb), 0.5);
        }
        40% {
          {% set s1 = 1.08 + vol * 0.1 %}
          transform: scale({{ s1 | round(2) }});
          box-shadow:
            0 0 0 8px rgba(var(--icon-color-rgb), 0.6),
            0 0 0 14px rgba(var(--icon-color-rgb), 0.3);
        }
        100% {
          transform: scale(1);
          box-shadow:
            0 0 0 20px rgba(var(--icon-color-rgb), 0),
            0 0 0 30px rgba(var(--icon-color-rgb), 0);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 0px 0px -18px !important;
        padding-right: 20px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>25 - Router</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:router-wireless
icon_color: blue
name: Router
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}
          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: wifi-waves 1.7s ease-out infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.6;
        {% endif %}

        position: relative;
      }

      @keyframes wifi-waves {
        0% {
          box-shadow:
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.9),
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.5),
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.2);
        }
        30% {
          box-shadow:
            0 0 0 4px rgba(var(--rgb-{{ config.icon_color }}), 0.6),
            0 0 0 10px rgba(var(--rgb-{{ config.icon_color }}), 0.35),
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.2);
        }
        60% {
          box-shadow:
            0 0 0 8px rgba(var(--rgb-{{ config.icon_color }}), 0.0),
            0 0 0 18px rgba(var(--rgb-{{ config.icon_color }}), 0.25),
            0 0 0 28px rgba(var(--rgb-{{ config.icon_color }}), 0.1);
        }
        100% {
          box-shadow:
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.0),
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.0),
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.0);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 0 10px -20px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>26 - Android TV</strong></summary>

```yaml
type: custom:mushroom-media-player-card
entity: media_player.android_tv
volume_controls:
  - volume_mute
  - volume_set
  - volume_buttons
media_controls:
  - on_off
  - shuffle
  - previous
  - play_pause_stop
  - next
  - repeat
show_volume_level: true
use_media_info: false
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ======= COLOR CONFIG ======= #}
        {% set icon_on_color      = 'red' %}
        {% set circle_on_rgb      = '255, 30, 30' %}     {# dark red-ish #}
        {% set circle_on_opacity  = 0.15 %}

        {% set icon_off_color     = 'grey' %}
        {% set circle_off_rgb     = '80, 80, 80' %}      {# dark grey #}
        {% set circle_off_opacity = 0.25 %}
        {# ============================ #}

        {# ========== USER CONFIG ========== #}

        {# STATE MODE SETTINGS: any state except 'off' will trigger #}
        {% set state_entity    = 'media_player.android_tv' %}
        {% set off_value       = 'off' %}

        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER LOGIC ======== #}
          {# any state except 'off' #}
          {% set trigger_active = (states(state_entity) != off_value) %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          {# ON / ACTIVE COLORS #}
          --icon-color: rgb(var(--rgb-{{ icon_on_color }}));
          --icon-color-rgb: var(--rgb-{{ icon_on_color }});

          --shape-color: rgba({{ circle_on_rgb }}, {{ circle_on_opacity }});
          --shape-color-rgb: {{ circle_on_rgb }};

          --shape-animation: bass-pulse 0.8s ease-out infinite;
          opacity: 1;
        {% else %}
          {# OFF / IDLE COLORS #}
          --icon-color: rgb(var(--rgb-{{ icon_off_color }}));
          --icon-color-rgb: var(--rgb-{{ icon_off_color }});

          --shape-color: rgba({{ circle_off_rgb }}, {{ circle_off_opacity }});
          --shape-color-rgb: {{ circle_off_rgb }};

          --shape-animation: none;
          opacity: 0.7;
        {% endif %}

        transform-origin: 50% 50%;
      }

      @keyframes bass-pulse {
        0% {
          transform: scale(1);
          box-shadow:
            0 0 0 0 rgba(var(--icon-color-rgb), 0.8);
        }
        10% {
          transform: scale(1.08);
          box-shadow:
            0 0 10px 4px rgba(var(--icon-color-rgb), 0.8);
        }
        25% {
          transform: scale(1.03);
          box-shadow:
            0 0 6px 2px rgba(var(--icon-color-rgb), 0.5);
        }
        50% {
          transform: scale(1.06);
          box-shadow:
            0 0 12px 5px rgba(var(--icon-color-rgb), 0.6);
        }
        100% {
          transform: scale(1);
          box-shadow:
            0 0 0 0 rgba(var(--icon-color-rgb), 0.0);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 0px 0px -15px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>27 - PIR Sensor</strong></summary>

```yaml
type: custom:mushroom-entity-card
entity: switch.plug_6_local
tap_action:
  action: toggle
icon: mdi:motion-sensor
icon_color: red
name: PIR Sensor
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# true = number mode, false = state mode #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'switch.plug_6_local' %}
        {% set active_value    = 'on' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.plug_power' %}
        
        {# '>' '<' '=' '>=' '<=' #}
        {% set number_operator = '>' %}
        
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}
          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: ultra-motion-active 1.3s linear infinite;
          --motion-cone-animation: ultra-motion-cone 1.6s linear infinite;
          --motion-pulse-animation: ultra-motion-pulse 1.3s ease-out infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          --motion-cone-animation: none;
          --motion-pulse-animation: none;
          opacity: 0.75;
        {% endif %}

        transform-origin: 50% 50%;
        position: relative;
      }

      .shape::before,
      .shape::after {
        content: '';
        position: absolute;
        inset: -6px;
        border-radius: inherit;
        pointer-events: none;
      }

      .shape::before {
        animation: var(--motion-cone-animation);
      }

      .shape::after {
        animation: var(--motion-pulse-animation);
      }

      @keyframes ultra-motion-active {
        0%   { transform: scale(1); }
        50%  { transform: scale(1.06); }
        100% { transform: scale(1); }
      }

      @keyframes ultra-motion-scan {
        0%   { transform: rotate(0deg); }
        100% { transform: rotate(360deg); }
      }

      @keyframes ultra-motion-cone {
        0% {
          box-shadow:
            0 -20px 14px -16px rgba(var(--rgb-{{ config.icon_color }}), 0.0);
        }
        25% {
          box-shadow:
            8px -16px 14px -16px rgba(var(--rgb-{{ config.icon_color }}), 0.5);
        }
        50% {
          box-shadow:
            0 -20px 14px -12px rgba(var(--rgb-{{ config.icon_color }}), 0.8);
        }
        75% {
          box-shadow:
            -8px -16px 14px -16px rgba(var(--rgb-{{ config.icon_color }}), 0.5);
        }
        100% {
          box-shadow:
            0 -20px 14px -16px rgba(var(--rgb-{{ config.icon_color }}), 0.0);
        }
      }

      @keyframes ultra-motion-pulse {
        0% {
          box-shadow:
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.9),
            0 0 0 0 rgba(var(--rgb-{{ config.icon_color }}), 0.4);
        }
        40% {
          box-shadow:
            0 0 0 8px rgba(var(--rgb-{{ config.icon_color }}), 0.5),
            0 0 0 14px rgba(var(--rgb-{{ config.icon_color }}), 0.2);
        }
        100% {
          box-shadow:
            0 0 0 22px rgba(var(--rgb-{{ config.icon_color }}), 0.0),
            0 0 0 30px rgba(var(--rgb-{{ config.icon_color }}), 0.0);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -15px 0 10px -18px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>28 - Lamp</strong></summary>

```yaml
type: custom:mushroom-light-card
entity: light.bedroom_lamp_local
use_light_color: true
show_color_temp_control: true
show_brightness_control: true
collapsible_controls: false
show_color_control: true
name: Lamp
icon_color: auto
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {% set state_entity    = 'light.bedroom_lamp_local' %}
        {% set active_value    = 'on' %}

        {# ====== TRIGGER ====== #}
          {% set trigger_active = (states(state_entity) == active_value) %}

        {# ====== COLOR FROM ENTITY (rgb_color) ====== #}
        {% set rgb = state_attr(config.entity, 'rgb_color') %}
        {% if rgb is not none %}
          {% set r = (rgb[0] | int) %}
          {% set g = (rgb[1] | int) %}
          {% set b = (rgb[2] | int) %}
        {% else %}
          {# fallback if lamp has no rgb_color (ct-only, etc) #}
          {% set r = 255 %}
          {% set g = 240 %}
          {% set b = 200 %}
        {% endif %}

        {% if trigger_active %}
          --shape-animation: lamp-glow 1.4s ease-in-out infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.5;
        {% endif %}
      }

      @keyframes lamp-glow {
        0% {
          filter: brightness(1);
          box-shadow: 0 0 6px 2px rgba({{ r }}, {{ g }}, {{ b }}, 0.6);
        }
        20% {
          filter: brightness(1.25);
          box-shadow: 0 0 14px 6px rgba({{ r }}, {{ g }}, {{ b }}, 0.9);
        }
        30% {
          filter: brightness(0.9);
          box-shadow: 0 0 3px 1px rgba({{ r }}, {{ g }}, {{ b }}, 0.4);
        }
        50% {
          filter: brightness(1.3);
          box-shadow: 0 0 16px 8px rgba({{ r }}, {{ g }}, {{ b }}, 1);
        }
        80% {
          filter: brightness(1.05);
          box-shadow: 0 0 8px 3px rgba({{ r }}, {{ g }}, {{ b }}, 0.7);
        }
        100% {
          filter: brightness(1);
          box-shadow: 0 0 6px 2px rgba({{ r }}, {{ g }}, {{ b }}, 0.6);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -22px 0px 0px -22px !important;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>29 - Garage Door</strong></summary>

```yaml
type: custom:mushroom-cover-card
entity: cover.garage_door
show_buttons_control: true
show_position_control: true
show_tilt_position_control: true
fill_container: false
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========= COLOR CONFIG ========= #}
        {% set cover_color = 'blue' %}   {# change to 'red', 'blue', etc. #}
        --cover-color-rgb: var(--rgb-{{ cover_color }});
        {# ================================= #}

        {# ========== USER CONFIG ========== #}
        {% set use_number      = false %}

        {# STATE MODE SETTINGS #}
        {% set state_entity    = 'cover.garage_door' %}
        {% set active_value    = 'open' %}

        {# OPTIONAL: NUMBER MODE SETTINGS #}
        {% set number_entity   = 'sensor.cover_position' %}
        {% set number_operator = '>' %}
        {% set threshold       = 0.0 %}
        {# ========== END USER CONFIG ====== #}

        {# ======== TRIGGER DECISION LOGIC ======== #}
        {% if use_number %}
          {% set num = states(number_entity) | float(0) %}

          {% if number_operator == '>' %}
            {% set trigger_active = (num > threshold) %}
          {% elif number_operator == '<' %}
            {% set trigger_active = (num < threshold) %}
          {% elif number_operator == '=' %}
            {% set trigger_active = (num == threshold) %}
          {% elif number_operator == '>=' %}
            {% set trigger_active = (num >= threshold) %}
          {% elif number_operator == '<=' %}
            {% set trigger_active = (num <= threshold) %}
          {% else %}
            {% set trigger_active = false %}
          {% endif %}
        {% else %}
          {% set trigger_active = (states(state_entity) == active_value) %}
        {% endif %}
        {# =========== END TRIGGER LOGIC =========== #}

        {% if trigger_active %}
          --shape-animation: motion-active 1.4s linear infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.7;
        {% endif %}

        transform-origin: 50% 50%;
      }

      @keyframes motion-active {
        0% {
          transform: scale(1);
          box-shadow:
            0 0 0 0 rgba(var(--cover-color-rgb), 0.9),
            0 0 0 0 rgba(var(--cover-color-rgb), 0.4);
        }
        40% {
          transform: scale(1.06);
          box-shadow:
            0 0 0 6px rgba(var(--cover-color-rgb), 0.5),
            0 0 0 12px rgba(var(--cover-color-rgb), 0.2);
        }
        100% {
          transform: scale(1);
          box-shadow:
            0 0 0 16px rgba(var(--cover-color-rgb), 0.0),
            0 0 0 24px rgba(var(--cover-color-rgb), 0.0);
        }
      }

      @keyframes motion-scan {
        0% {
          transform: rotate(0deg);
          box-shadow:
            0 -10px 0 0 rgba(var(--cover-color-rgb), 0.9);
        }
        50% {
          transform: rotate(180deg);
          box-shadow:
            0 10px 0 0 rgba(var(--cover-color-rgb), 0.6);
        }
        100% {
          transform: rotate(360deg);
          box-shadow:
            0 -10px 0 0 rgba(var(--cover-color-rgb), 0.9);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -15px 0 10px -18px !important;
        padding-right: 10px;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>

<details>
<summary><strong>30 - Lamp</strong></summary>

```yaml
type: custom:mushroom-light-card
entity: light.livingroom_lamp_local
use_light_color: true
show_color_temp_control: true
show_brightness_control: true
collapsible_controls: false
show_color_control: true
name: Lamp
icon_color: auto
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {% set state_entity    = 'light.livingroom_lamp_local' %}
        {% set active_value    = 'on' %}

        {# ======== TRIGGER ======== #}
          {% set trigger_active = (states(state_entity) == active_value) %}

        {# ======== COLOR FROM ENTITY (rgb_color) ======== #}
        {% set rgb = state_attr(config.entity, 'rgb_color') %}
        {% if rgb is not none %}
          {% set r = (rgb[0] | int) %}
          {% set g = (rgb[1] | int) %}
          {% set b = (rgb[2] | int) %}
        {% else %}
          {# fallback for CT-only lights #}
          {% set r = 255 %}
          {% set g = 240 %}
          {% set b = 200 %}
        {% endif %}

        {% if trigger_active %}
          --shape-animation: lamp-sweep 1.8s ease-in-out infinite;
          opacity: 1;
        {% else %}
          --shape-animation: none;
          opacity: 0.5;
        {% endif %}

        position: relative;
        transform-origin: 50% 60%;
      }

      @keyframes lamp-sweep {
        0% {
          filter: brightness(1.1);
          box-shadow:
            0 0 12px 4px rgba({{ r }}, {{ g }}, {{ b }}, 0.9),
            0 30px 40px -10px rgba({{ r }}, {{ g }}, {{ b }}, 0.0);
          transform: rotate(-4deg) scale(1);
        }
        25% {
          filter: brightness(1.4);
          box-shadow:
            0 0 18px 8px rgba({{ r }}, {{ g }}, {{ b }}, 1),
            -8px 35px 45px -12px rgba({{ r }}, {{ g }}, {{ b }}, 0.5);
          transform: rotate(3deg) scale(1.02);
        }
        50% {
          filter: brightness(1.2);
          box-shadow:
            0 0 24px 10px rgba({{ r }}, {{ g }}, {{ b }}, 0.9),
            8px 35px 45px -12px rgba({{ r }}, {{ g }}, {{ b }}, 0.5);
          transform: rotate(-2deg) scale(1.03);
        }
        75% {
          filter: brightness(1.5);
          box-shadow:
            0 0 18px 8px rgba({{ r }}, {{ g }}, {{ b }}, 1),
            -6px 30px 40px -12px rgba({{ r }}, {{ g }}, {{ b }}, 0.4);
          transform: rotate(2deg) scale(1.01);
        }
        100% {
          filter: brightness(1.1);
          box-shadow:
            0 0 12px 4px rgba({{ r }}, {{ g }}, {{ b }}, 0.8),
            0 30px 40px -10px rgba({{ r }}, {{ g }}, {{ b }}, 0.0);
          transform: rotate(-4deg) scale(1);
        }
      }
    .: |
      mushroom-shape-icon {
        --icon-size: 65px;
        display: flex;
        margin: -18px 0px 0px -18px !important;
      }
      ha-card {
        clip-path: inset(0 0 0 0 round var(--ha-card-border-radius, 12px));
      }

```
</details>
<details>
  <summary><strong>31 - EV Battery (Charging animation)</strong></summary>

  ![card31-ev-battery-preview](https://github.com/user-attachments/assets/90b2c0e3-ae1b-4e50-827c-94b56db9546e)

  ```yaml
type: custom:mushroom-entity-card
entity: sensor.ev_battery_level
name: EV Battery
icon: mdi:car-electric
icon_color: green
card_mod:
  style:
    mushroom-shape-icon$: |
      .shape {
        {# ========== USER CONFIG ========== #}
        {# Use number mode: battery percentage drives visuals #}
        {% set use_number      = true %}

        {# NUMBER MODE SETTINGS #}
        {# Battery level entity (0–100) #}
        {% set number_entity   = 'sensor.ev_battery_level' %}

        {# Threshold just controls when animation can be active #}
        {% set number_operator = '>' %}
        {% set threshold       = 0 %}

        {# Optional: only animate when charging #}
        {# Set to a real charging sensor or 'none' #}
        {% set charging_entity = 'binary_sensor.ev_charging' %}
        {# ========== END USER CONFIG ====== #}

        {# -------- TRIGGER DECISION LOGIC -------- #}
        {% set num = states(number_entity) | int(0) %}

        {% if number_operator == '>' %}
          {% set trigger_active = (num > threshold) %}
        {% elif number_operator == '<' %}
          {% set trigger_active = (num < threshold) %}
        {% elif number_operator == '=' %}
          {% set trigger_active = (num == threshold) %}
        {% elif number_operator == '>=' %}
          {% set trigger_active = (num >= threshold) %}
        {% elif number_operator == '<=' %}
          {% set trigger_active = (num <= threshold) %}
        {% else %}
          {% set trigger_active = false %}
        {% endif %}

        {# Optional: gate by charging-state if that entity exists #}
        {% if charging_entity != 'none' %}
          {% if states(charging_entity) not in ['on', 'charging'] %}
            {% set trigger_active = false %}
          {% endif %}
        {% endif %}
        {# ------ END TRIGGER DECISION LOGIC ------ #}

        {# Battery-level buckets #}
        {% set battery = num %}
        {% if battery <= 15 %}
          {% set level = 'low' %}
        {% elif battery <= 80 %}
</details>


---

[paypal_me_shield]: https://img.shields.io/badge/PayPal-00457C?style=for-the-badge&logo=paypal&logoColor=white

[paypal_me]: https://paypal.me/anasboxsupport

[revolut_me_shield]:
https://img.shields.io/badge/revolut-FFFFFF?style=for-the-badge&logo=revolut&logoColor=black

[revolut_me]: https://revolut.me/anas4e

[ko_fi_shield]: https://img.shields.io/badge/Ko--fi-F16061?style=for-the-badge&logo=ko-fi&logoColor=white

[ko_fi_me]: https://ko-fi.com/anasbox

[buy_me_coffee_shield]: 
https://img.shields.io/badge/Buy%20Me%20Coffee-ffdd00?style=for-the-badge&logo=buy-me-a-coffee&logoColor=black

[buy_me_coffee_me]: https://www.buymeacoffee.com/anasbox

[patreon_shield]: 
https://img.shields.io/badge/patreon-404040?style=for-the-badge&logo=patreon&logoColor=white

[patreon_me]:  https://patreon.com/AnasBox
