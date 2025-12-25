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
    ├── README.md             # Nodes Documentation
    └── living_room_node.yaml # The Node Logic (ESPHome Config)
```

## Architecture
- **Host:** Fedora Server
- **Container Runtime:** Docker (Compose)
- **Networking:** Host Mode (Required for mDNS/Discovery)
- **Hardware:** NodeMCU ESP8266 + BME280 (I2C) + PIR (GPIO)

## Setup Instructions

### 1. Prerequisites
- Fedora/Linux host with Docker installed.
- USB-to-Serial drivers (standard on Linux).

### 2. Configuration & Secrets

1.  **Prepare Directory:**
    The infrastructure is defined in `infra/smarthome.yml`. By default, it creates configuration directories relative to itself.
    
2.  **Create Secrets:**
    ESPHome requires a `secrets.yaml` file.
    Create `secrets.yaml` inside the configuration directory that will be mounted by ESPHome (e.g., `infra/esphome_config/` after first run, or manually create it).
    
    ```yaml
    # secrets.yaml
    wifi_ssid: "MyWiFi"
    wifi_password: "password123"
    ap_password: "fallback_password"
    ```

3.  **Deploy Nodes:**
    Copy the node configurations from `nodes/` to your active ESPHome configuration directory (e.g., `infra/esphome_config/`).

### 3. Running the stack

Navigate to the infrastructure directory and start the services:

```bash
cd infra
docker compose -f smarthome.yml up -d
```

### 4. Flashing a New Node (Local USB)
Connect NodeMCU to the server via USB. Ensure the node configuration file is present in the container's `/config` directory (mapped volume).

```bash
# Assuming you are running the command from the directory containing the compose file
docker exec -it esphome esphome run living_room_node.yaml
```

## Hardware Pinout (Living Room Node)
- **SDA (Data):** D2
- **SCL (Clock):** D1
- **PIR Sensor:** D5
- **Power:** 3V3 (Do not use Vin for BME280)

