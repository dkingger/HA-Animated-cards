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
          {% if states(charging_entity) not in ['on', 'charging', 'true', '1'] %}
            {% set trigger_active = false %}
          {% endif %}
        {% endif %}
        {# ------ END TRIGGER DECISION LOGIC ------ #}

        {# Battery-level buckets #}
        {% set battery = num %}
        {% if battery <= 15 %}
          {% set level = 'low' %}
        {% elif battery <= 80 %}
          {% set level = 'medium' %}
        {% else %}
          {% set level = 'high' %}
        {% endif %}

        {# Visual rules based on level and charging #}
        {% if trigger_active %}
          {% if level == 'low' %}
            --shape-animation: battery-charge-slow 1.6s ease-in-out infinite;
            --battery-glow: 0 0 10px 4px rgba(var(--rgb-{{ config.icon_color }}), 0.9);
            opacity: 1;
          {% elif level == 'medium' %}
            --shape-animation: battery-charge 1.2s ease-in-out infinite;
            --battery-glow: 0 0 14px 6px rgba(var(--rgb-{{ config.icon_color }}), 0.95);
            opacity: 1;
          {% else %}
            --shape-animation: battery-charge-fast 0.9s linear infinite;
            --battery-glow: 0 0 18px 8px rgba(var(--rgb-{{ config.icon_color }}), 1);
            opacity: 1;
          {% endif %}
        {% else %}
          --shape-animation: none;
          --battery-glow: none;
          opacity: 0.7;
        {% endif %}

        position: relative;
        transform-origin: 50% 50%;
      }

      /* Simple charging animations */
      @keyframes battery-charge-slow {
        0%   { transform: translateY(0) scale(1); box-shadow: var(--battery-glow); }
        50%  { transform: translateY(-2px) scale(1.02); box-shadow: 0 0 8px 3px rgba(var(--rgb-{{ config.icon_color }}), 0.6); }
        100% { transform: translateY(0) scale(1); box-shadow: var(--battery-glow); }
      }
      @keyframes battery-charge {
        0%   { transform: translateY(0) scale(1); box-shadow: var(--battery-glow); }
        50%  { transform: translateY(-3px) scale(1.03); box-shadow: 0 0 12px 5px rgba(var(--rgb-{{ config.icon_color }}), 0.8); }
        100% { transform: translateY(0) scale(1); box-shadow: var(--battery-glow); }
      }
      @keyframes battery-charge-fast {
        0%   { transform: translateY(0) scale(1); box-shadow: var(--battery-glow); }
        50%  { transform: translateY(-4px) scale(1.04); box-shadow: 0 0 18px 8px rgba(var(--rgb-{{ config.icon_color }}), 1); }
        100% { transform: translateY(0) scale(1); box-shadow: var(--battery-glow); }
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