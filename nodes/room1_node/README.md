# Room1 Multi-Sensor Node

This node is designed for comprehensive environmental monitoring and occupancy detection in "Room 1". It utilizes the compact Seeed Studio XIAO ESP32C3 microcontroller.

## Hardware Specifications

- **Microcontroller:** Seeed Studio XIAO ESP32C3
- **Communication:** WiFi (2.4GHz)
- **Power:** USB-C (Continuous power required due to active motion monitoring)

### Sensors

| Sensor | Type | Interface | Address/Pin | Function |
| :--- | :--- | :--- | :--- | :--- |
| **BME280** | Environmental | I2C | `0x76` | Measures Temperature & Pressure |
| **BH1750** | Light | I2C | `0x23` | Measures Ambient Light Level (Lux) |
| **PIR** | Motion | GPIO | `GPIO3` | Detects room occupancy/motion |

## Pinout Configuration

Based on `room1_node.yaml`:

- **I2C Bus:**
  - **SDA:** GPIO6 (Physical Pin D4)
  - **SCL:** GPIO7 (Physical Pin D5)
- **Motion Sensor:**
  - **Signal:** GPIO3 (Physical Pin D1)

## Software Features

- **Anti-Ghost Filter:** The PIR sensor configuration includes filters to ignore momentary spikes (radio interference) and prevent rapid toggling, ensuring stable motion detection.
- **WiFi Power Mode:** `power_save_mode: none` is set to ensure the lowest latency for motion detection and stability for the PIR sensor.

## Setup

1.  **Secrets:** Ensure a `secrets.yaml` file exists in the root of your ESPHome project directory containing:
    ```yaml
    wifi_ssid: "YOUR_SSID"
    wifi_password: "YOUR_PASSWORD"
    ```
2.  **Flashing:** Connect the XIAO ESP32C3 via USB and run the ESPHome dashboard or CLI to flash `room1_node.yaml`.
