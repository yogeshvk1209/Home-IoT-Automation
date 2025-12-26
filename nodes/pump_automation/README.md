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
### 1. High Voltage Components (Mains Side)
| Component | Specification | Quantity | Purpose |
| :--- | :--- | :--- | :--- |
| **MOV (Snubber)** | 14D471K (Metal Oxide Varistor) | 1 | Absorbs voltage spikes to protect Relay & ESP |
| **Relay Module** | 30A / 250V (Songle SLA-05VDC-SL-C) | 1 | Switches the Pump (Rated 1.5HP) |
| **Connectors** | Wire Nuts (Red/Orange) OR Wago 221-415 | 3 | Main junctions for "Forking" the wires |
| **Power Supply** | Hi-Link PM01 (5V) OR USB Phone Charger | 1 | Powers the ESP8266 from 220V Mains |
| **Terminal Strip**| 15A Connector Strip (Optional) | 1 | Alternative to Wire Nuts if preferred |

### 2. Low Voltage Components (Logic Side)
| Component | Specification | Quantity | Purpose |
| :--- | :--- | :--- | :--- |
| **Controller** | ESP8266 (NodeMCU v2 or D1 Mini) | 1 | The "Brain" running ESPHome |
| **Wires** | Dupont Jumper Wires (Female-Female) | 3 | Connects ESP to Relay (5V, GND, D1) |

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
