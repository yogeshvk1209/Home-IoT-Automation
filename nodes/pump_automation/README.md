# 1HP Smart Water Pump Controller (Parallel Bypass)

This project controls a 1HP AC Water Pump using an ESP8266 and Home Assistant. It features a "Fail-Safe" parallel wiring architecture that allows the existing manual wall switch to function even if the smart system fails completely.

## ⚠️ Safety Warning
**220V AC Mains Voltage.** Risk of electric shock or fire.
- Always unplug mains before working.
- Use Wago/Wire Nut connectors for high-current paths.
- **NEVER** use breadboards for 220V.

## Architecture
**"Parallel Bypass" Logic:**
The Relay and the Manual Wall Switch are wired in PARALLEL.
- If **Manual Switch** is ON → Pump RUNS (Overrides Smart system).
- If **Smart Relay** is ON → Pump RUNS.
- To turn Pump OFF, **BOTH** must be OFF.

## Hardware BOM
| Component | Spec | Qty | Location |
| :--- | :--- | :--- | :--- |
| **NodeMCU** | ESP8266 | 1 | Inside Box |
| **Relay Module** | 30A / 250V (Songle SLA-05VDC-SL-C) | 1 | Inside Box |
| **MOV (Snubber)**| 14D471K (Varistor) | 1 | Across Pump Terminals |
| **Power Supply** | Hi-Link PM01 or 5V Phone Charger | 1 | Mains -> ESP 5V |
| **Connectors** | Wire Nuts (Red/Orange) or Wago 221-415 | 3 | Mains Junctions |

## Wiring Guide (Phase 2)

### 1. High Voltage Connections (The "Forks")

**Junction A: Live Input (Red Wire Nut)**
Twist these 4 wires together:
1.  Incoming Mains Live (From Wall)
2.  Wire to **Manual Switch Input**
3.  Wire to **Relay COM**
4.  Wire to **Power Supply Live** (Charger/Hi-Link)

**Junction B: Live Output (Red Wire Nut)**
Twist these 4 wires together:
1.  Wire from **Manual Switch Output**
2.  Wire from **Relay NO** (Normally Open)
3.  Wire to **Pump Live**
4.  **MOV Leg 1**

**Junction C: Neutral Hub (Red Wire Nut)**
Twist these 4 wires together:
1.  Incoming Mains Neutral
2.  Wire to **Pump Neutral**
3.  Wire to **Power Supply Neutral** (Charger/Hi-Link)
4.  **MOV Leg 2**

### 2. Low Voltage Connections (Soldered/Dupont)
- **Power:** Charger 5V → ESP Vin | Charger GND → ESP GND
- **Relay Power:** ESP Vin → Relay VCC | ESP GND → Relay GND
- **Relay Signal:** ESP D1 (GPIO5) → Relay IN

## Software Setup

### ESPHome Config (`pump_controller.yaml`)
See `pump_controller.yaml` in this repo for the flashable firmware.

### Home Assistant "Variable Timer" (Goal 3)
To allow "Run for X minutes" from Dashboard:

**1. Create Helper:**
- Type: `input_number`
- Name: `pump_timer_minutes`
- Min: 5, Max: 60, Step: 5

**2. Create Script:**
```yaml
alias: Run Pump with Timer
sequence:
  - service: switch.turn_on
    target:
      entity_id: switch.water_pump_relay
  - delay:
      minutes: "{{ states('input_number.pump_timer_minutes') | int }}"
  - service: switch.turn_off
    target:
      entity_id: switch.water_pump_relay
