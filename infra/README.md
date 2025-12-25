# Infrastructure Setup

This directory contains the core infrastructure for the smart home automation system, managed using Docker Compose.

## Services

The infrastructure consists of two primary services:

- **Home Assistant:** The central hub for home automation, providing a dashboard and logic for controlling devices.
- **ESPHome:** A tool for creating custom firmware for ESP8266/ESP32 boards and managing them over the air or via USB.

## Prerequisites

- **Docker:** Ensure Docker is installed on your host system.
- **Docker Compose:** Typically included with Docker Desktop or available as a plugin on Linux.
- **Hardware Access:** The host should have USB ports available if you intend to flash devices locally via ESPHome.

## Configuration

The services are defined in `smarthome.yml`. Key configuration details include:

### Networking
Both services use `network_mode: host`. This is critical for:
- **mDNS/Discovery:** Allows Home Assistant and ESPHome to automatically find devices on your local network.
- **Ease of Use:** Simplifies communication without complex port mapping.

### Volumes
Data is persisted in local directories to ensure configurations are kept across container restarts:
- `./ha_config`: Home Assistant configuration and database.
- `./esphome_config`: ESPHome node configurations and temporary build files.

### USB Access (ESPHome)
The ESPHome service is configured to access `/dev/ttyUSB0` on the host. This allows you to flash ESP boards directly from the web interface or CLI.
- **Note:** Ensure your user has permissions to access serial ports (usually by being in the `dialout` or `uucp` group on Linux).

## Usage

### Starting the Stack

Navigate to this directory and run:

```bash
docker compose -f smarthome.yml up -d
```

### Stopping the Stack

```bash
docker compose -f smarthome.yml down
```

### Viewing Logs

To monitor the logs for both services:

```bash
docker compose -f smarthome.yml logs -f
```

## First Run Setup

1. **Home Assistant:** Once the container is running, access the web UI at `http://<host-ip>:8123`.
2. **ESPHome:** Access the dashboard at `http://<host-ip>:6052`.
3. **Secrets:** Don't forget to create a `secrets.yaml` inside `esphome_config/` as detailed in the root `README.md`.
