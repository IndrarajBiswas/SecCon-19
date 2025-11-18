# SecCon-19: IoT Security Research

A collection of educational security research documentation focused on IoT device vulnerabilities and exploitation techniques. This repository was created for SecCon 2019 to demonstrate common security weaknesses in consumer IoT devices.

## Disclaimer

**This repository is for educational and authorized security research purposes only.**

- Only use these techniques on devices you own or have explicit permission to test
- Unauthorized access to computer systems and networks is illegal
- The authors are not responsible for any misuse of this information
- Always follow responsible disclosure practices when finding vulnerabilities

## Table of Contents

- [Project Overview](#project-overview)
- [Project Structure](#project-structure)
- [Modules](#modules)
  - [BLE Smart Bulb Exploitation](#ble-smart-bulb-exploitation)
  - [Sonoff Smart Switch Exploitation](#sonoff-smart-switch-exploitation)
  - [BLE Smart Lock](#ble-smart-lock)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
- [Contributing](#contributing)
- [References](#references)

## Project Overview

This project documents real-world security vulnerabilities in popular IoT devices, specifically:

- **BLE (Bluetooth Low Energy) devices** - Demonstrating Man-in-the-Middle attacks on smart bulbs
- **WiFi-based IoT devices** - Hardware-level exploitation of Sonoff smart switches

The goal is to raise awareness about IoT security issues and help security researchers understand common attack vectors.

## Project Structure

```
SecCon-19/
├── README.md                 # This file - Project overview
├── ble_bulb/                 # BLE Smart Bulb exploitation guide
│   ├── README.md             # Detailed MiTM attack documentation
│   └── Images/               # Supporting screenshots and diagrams
├── ble_lock/                 # BLE Smart Lock research (WIP)
│   └── README.md             # Module documentation
└── smart_switch/             # Sonoff device exploitation guide
    ├── README.md             # Firmware flashing documentation
    └── images/               # Pinout diagrams and screenshots
```

## Modules

### BLE Smart Bulb Exploitation

**Location:** [`ble_bulb/`](./ble_bulb/)

Demonstrates a Man-in-the-Middle (MiTM) attack on Bluetooth Low Energy smart bulbs using Gattacker.

**Key Concepts:**
- BLE advertisement packet capture
- Device emulation and spoofing
- Traffic interception and modification
- Replay attacks

**Attack Flow:**
1. Scan for target BLE device
2. Clone device advertisements and services
3. Trick mobile app to connect to cloned device
4. Intercept and modify communication
5. Replay captured commands

[View Full Documentation](./ble_bulb/README.md)

---

### Sonoff Smart Switch Exploitation

**Location:** [`smart_switch/`](./smart_switch/)

Demonstrates hardware-level exploitation of Sonoff Basic smart switches through firmware modification.

**Key Concepts:**
- UART serial communication
- Firmware extraction and backup
- Custom firmware flashing (Tasmota)
- Network device discovery

**Attack Flow:**
1. Connect to device UART pins via FTDI/USB-TTL converter
2. Backup original firmware
3. Flash custom firmware
4. Access device web interface
5. Control device and extract credentials

[View Full Documentation](./smart_switch/README.md)

---

### BLE Smart Lock

**Location:** [`ble_lock/`](./ble_lock/)

Research on BLE smart lock vulnerabilities.

**Status:** Documentation in progress

[View Documentation](./ble_lock/README.md)

## Prerequisites

### Software Requirements

| Tool | Purpose | Installation |
|------|---------|-------------|
| Node.js 8.x | Gattacker framework | `curl -sL https://deb.nodesource.com/setup_8.x \| sudo -E bash && sudo apt install nodejs` |
| Gattacker | BLE MiTM tool | `npm install gattacker` |
| Bleno/Noble | BLE Node.js modules | `npm install bleno noble` |
| Python 3 | esptool dependency | `sudo apt install python3` |
| esptool | Firmware flashing | [GitHub Releases](https://github.com/espressif/esptool/releases) |
| nmap | Network scanning | `sudo apt install nmap` |
| Bluetooth packages | BLE support | `sudo apt install bluetooth bluez libbluetooth-dev libudev-dev` |

### Hardware Requirements

- **For BLE attacks:**
  - 2x Bluetooth Low Energy adapters (USB dongles)
  - 2x Linux machines (can be VMs or Raspberry Pi)

- **For Sonoff attacks:**
  - USB to TTL converter (PL2303 or FTDI)
  - Soldering equipment (optional, for header pins)
  - Jumper wires

## Getting Started

### BLE Smart Bulb Attack

1. **Set up two machines** (host and slave) with Gattacker installed
2. **Configure Gattacker** by editing `config.env` with your HCI device IDs
3. **On slave machine:** Start the WebSocket slave service
   ```bash
   node ws-slave.js
   ```
4. **Scan for target device:**
   ```bash
   sudo node scan "DEVICE-NAME"
   ```
5. **On host machine:** Clone and broadcast device
   ```bash
   sudo ./mac_adv -a devices/[MAC].adv.json -s devices/[MAC].srv.json
   ```
6. **Connect with mobile app** - traffic will be intercepted
7. **Replay captured commands:**
   ```bash
   sudo node replay.js -i dump/[MAC].log -p [MAC] -s devices/[MAC].srv.json
   ```

See [detailed instructions](./ble_bulb/README.md)

### Sonoff Smart Switch Attack

1. **Install tools:**
   ```bash
   sudo apt install nmap python3
   ```
2. **Download esptool** from [GitHub](https://github.com/espressif/esptool/releases)
3. **Connect device** via USB-TTL converter (3.3V, TX, RX, GND)
4. **Backup firmware:**
   ```bash
   sudo ./esptool.py --port /dev/ttyUSB0 read_flash 0x00000 0x100000 backup.bin
   ```
5. **Flash custom firmware:**
   ```bash
   sudo ./esptool.py --port /dev/ttyUSB0 write_flash -fs 1MB -fm dout 0x0 sonoff.bin
   ```
6. **Connect to device hotspot** and configure WiFi
7. **Find device IP** using nmap and access web interface

See [detailed instructions](./smart_switch/README.md)

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Submit a pull request with clear description

When contributing:
- Follow existing documentation format
- Include screenshots/diagrams where helpful
- Test all procedures before documenting
- Update this README if adding new modules

## References

- [Gattacker - BLE MiTM Tool](https://github.com/securing/gattacker)
- [Tasmota Firmware](https://github.com/arendst/Tasmota)
- [esptool - Espressif Flasher](https://github.com/espressif/esptool)
- [Noble - BLE Central Module](https://github.com/noble/noble)
- [Bleno - BLE Peripheral Module](https://github.com/noble/bleno)

## License

This project is for educational purposes. Please use responsibly and ethically.

---

**SecCon 2019** - Security Conference IoT Research
