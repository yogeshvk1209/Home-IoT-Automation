# Node Configurations

This directory contains the ESPHome YAML configuration files for the smart home sensor nodes.

## Files

- `living_room_node.yaml`: Configuration for the Living Room Node (NodeMCU ESP8266).

## Usage

These configurations are intended to be used with the ESPHome container defined in `../infra/smarthome.yml`.

### Secrets

The configurations reference a `secrets.yaml` file for sensitive information (WiFi credentials, passwords). You must create a `secrets.yaml` file in the same directory where these node configurations are placed/run.

**`secrets.yaml` template:**

```yaml
wifi_ssid: "YOUR_WIFI_SSID"
wifi_password: "YOUR_WIFI_PASSWORD"
ap_password: "FALLBACK_AP_PASSWORD"
```

## Hardware Pinout (Living Room Node)

Based on `living_room_node.yaml`:

- **Board:** NodeMCU v2 (ESP8266)
- **I2C Bus:**
  - **SDA (Data):** D2
  - **SCL (Clock):** D1
- **Sensors:**
  - **BME280 (Temp/Hum/Press):** Address `0x76` (I2C)
  - **PIR Motion Sensor:** Pin D5
