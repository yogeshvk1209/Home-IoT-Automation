# My Smart Home Infrastructure

This repository contains the Infrastructure-as-Code (IaC) for my Home Assistant and ESPHome setup running on Fedora Server.

## Folder Structure

```
.
├── .gitignore
├── README.md                 # Project Documentation
├── infra/
│   └── smarthome.yml         # Docker Compose Infrastructure Definition
└── nodes/
    ├── secrets.yaml.example  # Template for secrets
    ├── living_room_node/     # Living Room Node (ESP8266 + BME280 + PIR)
    ├── pump_automation/      # Water Pump Controller (ESP8266 + Relay)
    ├── room1_node/           # Room 1 Multi-Sensor (ESP32C3 + BME280 + BH1750 + PIR)
    └── tank_monitor/         # Water Tank Level Monitor (ESP8266 + Float Switches)
```

## Architecture
- **Host:** Fedora Server
- **Container Runtime:** Docker (Compose)
- **Networking:** Host Mode (Required for mDNS/Discovery)
- **Hardware:** Various ESP8266 (NodeMCU) and ESP32 (Seeed XIAO) nodes.

## Available Nodes

| Node Name | Hardware | Key Sensors/Functions | Documentation |
| :--- | :--- | :--- | :--- |
| **Living Room Node** | ESP8266 NodeMCU | Temp/Hum/Press (BME280), Motion (PIR) | [Read More](nodes/living_room_node/README.md) |
| **Pump Automation** | ESP8266 NodeMCU | 1HP Pump Control (Relay), Manual Bypass | [Read More](nodes/pump_automation/README.md) |
| **Room 1 Node** | Seeed XIAO ESP32C3 | Temp/Press (BME280), Light (BH1750), Motion (PIR) | [Read More](nodes/room1_node/README.md) |
| **Tank Monitor** | ESP8266 NodeMCU | Water Level (Float Switches), Deep Sleep | [Read More](nodes/tank_monitor/README.md) |

## Setup Instructions

### 1. Prerequisites
- Fedora/Linux host with Docker installed.
- USB-to-Serial drivers (standard on Linux).

### 2. Configuration & Secrets

1.  **Prepare Directory:**
    The infrastructure is defined in `infra/smarthome.yml`.
    
2.  **Create Secrets:**
    ESPHome requires a `secrets.yaml` file.
    Create `secrets.yaml` inside the configuration directory that will be mounted by ESPHome (or where you run the dashboard). You can use `nodes/secrets.yaml.example` as a template.
    
    ```yaml
    # secrets.yaml
    wifi_ssid: "MyWiFi"
    wifi_password: "password123"
    ```

3.  **Deploy Nodes:**
    Copy the relevant node configuration file (e.g., `nodes/room1_node/room1_node.yaml`) to your active ESPHome configuration directory.

### 3. Running the stack

Navigate to the infrastructure directory and start the services:

```bash
cd infra
docker compose -f smarthome.yml up -d
```

### 4. Flashing a New Node (Local USB)
Connect the microcontroller to the server via USB. Ensure the node configuration file is present in the container's `/config` directory (mapped volume).

```bash
# Example for flashing the Living Room Node
docker exec -it esphome esphome run living_room_node.yaml
```