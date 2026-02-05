# Technical Architecture: Professional RFID Jukebox

This document outlines the hardware integration and software stack for the Jukebox project, running on an industrial-grade Linux distribution built via the Yocto Project (Scarthgap).

## 1. Hardware Specification

The system is hosted on a **Raspberry Pi 3 Model B**. It utilizes the SPI bus for RFID communication and discrete GPIOs for user feedback and power management.

### 1.1 GPIO & Peripheral Mapping

The following table defines the physical-to-logical mapping of the components.

| Component | Interface | BCM Pin | Physical Pin | Direction | Description |
| --- | --- | --- | --- | --- | --- |
| **MFRC522** | SPI0 | 7, 8, 9, 10, 11 | 24, 21, 19, 23, 26 | In/Out | RFID Reader (MISO, MOSI, SCLK, CE, RST) |
| **Green LED** | GPIO | 6 | 31 | Output | System Ready / Idle State |
| **Blue LED** | GPIO | 12 | 32 | Output | Playback Active (Blinking) |
| **Red LED** | GPIO | 5 | 29 | Output | Error / Unauthorized Tag |
| **Shutdown** | GPIO | 13 | 33 | Input | Pull-high; triggers halt on falling edge |

## 2. Software Stack

The system is built using a custom Yocto image to minimize the attack surface and optimize boot time.

### 2.1 Operating System Layers

* **Poky (Scarthgap):** The base build system and core metadata.
* **meta-raspberrypi:** Provides Board Support Package (BSP) and firmware.
* **meta-python:** Required for third-party Python modules.
* **meta-jukebox:** Custom application layer containing the logic and service definitions.

### 2.2 Application Logic

The application is written in **Python 3.12** utilizing the `asyncio` framework. This allows the system to concurrently:

1. Poll the SPI bus for RFID tags without blocking.
2. Manage LED blink patterns.
3. Monitor the `cvlc` subprocess for audio playback status.

## 3. System Orchestration

Process management is handled by `systemd`. The application is split into two distinct services to ensure the shutdown hook remains responsive even if the primary application encounters a logic error.

### 3.1 Service Hierarchy

* **`jukebox.service`**: Manages the main Python application and the VLC media player.
* **`shutdown-button.service`**: A lightweight background listener for the GPIO 13 interrupt.

### 3.2 Deployment Paths

| Path | Description |
| --- | --- |
| `/usr/bin/jukebox/` | Application binaries and scripts |
| `/usr/share/jukebox/music/` | Root directory for audio folders |
| `/etc/jukebox/mappings.cfg` | Configuration for Tag UID to Folder mapping |
| `/var/log/jukebox/` | Persistent logs for system health and playback tracking |

## 4. Build & Reproducibility

The project uses `kas` for build orchestration. This ensures that the exact versions of all layers, the Linux kernel, and the compiler are pinned, allowing any developer to reproduce the exact same production image from the manifest file.
