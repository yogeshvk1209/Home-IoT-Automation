# Smart Tank Monitor

This node monitors the water level in an overhead tank using two float switches. It is designed for low-power operation, utilizing Deep Sleep to conserve battery life (if battery powered) or minimize active time.

## Hardware Specifications

- **Microcontroller:** NodeMCU v2 (ESP8266)
- **Sensors:** 2x Magnetic Float Switches (Non-contact/Waterproof type recommended)

## Logic & Operation

The system interprets the state of two vertical float switches to determine the approximate water level.

| Top Sensor | Mid Sensor | Status Output |
| :--- | :--- | :--- |
| **ON** (Floating) | (Any) | **100% (Full)** |
| OFF | **ON** (Floating) | **50% (Half)** |
| OFF | OFF | **Low / Critical** |

*Note: The logic assumes `invert: true` means the switch is CLOSED (Ground) when floating (Wet).*

## Power Management (Deep Sleep)

To save energy, this node operates on a duty cycle:
- **Run Duration:** 30 seconds (Connects to WiFi, reads sensors, reports state)
- **Sleep Duration:** 30 minutes (Deep Sleep)

*Note: For the ESP8266 to wake up from Deep Sleep, **GPIO16 (D0)** must be connected to **RST**.*

## Pinout Configuration

Based on `tank_monitor.yaml`:

- **Tank Top Sensor (100%):** GPIO D2
- **Tank Mid Sensor (50%):** GPIO D5
- **Deep Sleep Wakeup:** Connect D0 to RST

## Configuration

### WiFi Setup
Currently, `tank_monitor.yaml` has placeholder credentials. You should update the WiFi section in the YAML file or, preferably, switch to using `!secret` substitution like other nodes.

**Current (Hardcoded):**
```yaml
wifi:
  ssid: "YOUR_EXTENDER_SSID"
  password: "YOUR_PASSWORD"
```

**Recommended (Secrets):**
1.  Update yaml to use `ssid: !secret wifi_ssid`
2.  Define `wifi_ssid` in your `secrets.yaml`.
