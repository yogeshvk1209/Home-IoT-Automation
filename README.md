# My Smart Home Infrastructure

This repository contains the Infrastructure-as-Code (IaC) for my Home Assistant and ESPHome setup running on Fedora Server.
Folder Structure

my-smart-home/
├── .gitignore                # Vital: Ignores secrets and build artifacts
├── README.md                 # Documentation
├── docker-compose.yml        # Infrastructure Definition
├── esphome/
│   ├── secrets.yaml          # [IGNORED] Actual passwords
│   ├── secrets.yaml.example  # Template for other users/backup
│   └── living_room_node.yaml # The Node Logic
└── homeassistant/            # (Optional) HA Config backups

## Architecture
- **Host:** Fedora Server
- **Container Runtime:** Docker (Compose)
- **Networking:** Host Mode (Required for mDNS/Discovery)
- **Hardware:** NodeMCU ESP8266 + BME280 (I2C) + PIR (GPIO)

## Directory Structure
- `/esphome`: Contains YAML configurations for sensor nodes.
- `/homeassistant`: Mapped volume for HA configuration.

## Setup Instructions

### 1. Prerequisites
- Fedora/Linux host with Docker installed.
- USB-to-Serial drivers (standard on Linux).

### 2. Deployment
```bash
# Clone the repo
git clone <your-repo-url>
cd my-smart-home

# Create Secrets
cp esphome/secrets.yaml.example esphome/secrets.yaml
nano esphome/secrets.yaml # Fill in WiFi credentials
```

### 3. Running the stack

```bash
docker compose up -d
```

### 4. Flashing a New Node (Local USB)
Connect NodeMCU to the server via USB.
```bash
docker exec -it esphome esphome run living_room_node.yaml
```

### 5. Hardware Pinout (Living Room Node)
SDA (Data): D1
SCL (Clock): D2
PIR Sensor: D5
Power: 3V3 (Do not use Vin for BME280)

### 6. Execution Plan (Wrap Up)**

Run these commands to lock it in:

1.  **Move files:** Organize your current random files into the structure above.
2.  **Create Secrets:** `mv` your credentials into `secrets.yaml`.
3.  **Initialize Git:**
    ```bash
    cd ~/smarthome
    git init
    git add .
    git commit -m "Initial commit: Working Living Room Node with Docker Stack"
    ```

You now have a clean, version-controlled, secure IoT stack.

**Would you like me to generate a specific `docker-compose.yml` for this repo that includes the `secrets.yaml` mapping, or are you good to build the repo yourself?**

