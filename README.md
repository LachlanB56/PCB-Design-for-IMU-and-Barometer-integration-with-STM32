# PCB-Design-for-IMU-and-Barometer-integration-with-STM32
# I2C IMU + Barometer Breakout Board

A simple two-layer PCB designed in Altium Designer that acts as a connection hub between an **STM32 NUCLEO-F411RE** development board and two I2C sensors: an **MPU6050** (6-axis accelerometer/gyroscope) and a **BMP280** (barometric pressure + temperature). The board breaks out a shared I2C bus to pin headers so the Nucleo and both sensor modules connect with standard 2.54 mm jumper cables — no soldering of fine-pitch chips required.

This was built as a learning project to practice the full schematic-to-layout workflow in Altium: symbol/footprint management, net definition, routing, design-rule checking, and fabrication output.
<img width="673" height="456" alt="Screenshot 2026-06-04 071927" src="https://github.com/user-attachments/assets/25c637af-b57c-487e-81ad-5023446d1984" />

## Overview

| | |
|---|---|
| **MCU** | STM32 NUCLEO-F411RE |
| **Sensors** | MPU6050 (GY-521 module), BMP280 (GY-BMP280-3.3 module) |
| **Interface** | I2C (single shared bus) |
| **Logic / supply** | 3.3 V |
| **Layers** | 2 (top + bottom) |
| **Connectors** | 2.54 mm through-hole pin headers (Samtec TSW series) |
| **EDA tool** | Altium Designer 26 |

## How it works

Both sensors are I2C devices, so they share a single bus. The board ties the two sensors' SCL lines together, both SDA lines together, and common VCC (3.3 V) and GND rails, then runs that bus back to a header that connects to the Nucleo's I2C1 pins (PB8 = SCL, PB9 = SDA). Because the two sensors have different addresses, they coexist on the same bus without conflict:

| Device | I2C address | Address set by |
|---|---|---|
| MPU6050 | 0x68 | AD0 tied to GND |
| BMP280 | 0x76 | SDO tied to GND |

The BMP280's CSB pin is tied to 3.3 V to select I2C mode. A bulk capacitor sits across the incoming 3.3 V rail to stabilize power delivered over the jumper cables.

## Connections

The Nucleo header carries the minimum four lines needed for the bus:

- **3V3** — powers both sensor modules
- **GND** — common ground / signal reference
- **SCL** — I2C clock (Nucleo PB8)
- **SDA** — I2C data (Nucleo PB9)

<img width="452" height="274" alt="Screenshot 2026-06-04 072611" src="https://github.com/user-attachments/assets/2c31ca24-1310-47d8-9385-dbd18f1f2458" />

<img width="482" height="332" alt="Screenshot 2026-06-04 104208" src="https://github.com/user-attachments/assets/b9a6450d-caa6-48ce-942d-92a84b572957" />

## Components

| Designator | Part | Notes |
|---|---|---|
| U1 | GY-521 (MPU6050) header | 1x8, 2.54 mm — module plugs in via header |
| J2 | GY-BMP280 header | 1x6, 2.54 mm — module plugs in via header |
| J1 | Nucleo I2C header | 1x4, 2.54 mm — 3V3 / GND / SCL / SDA |
| R1, R2 | I2C pull-up resistors | SDA and SCL to 3.3 V (see note below) |
| C1 | Decoupling capacitor | Bulk cap across the 3.3 V rail |

> **Note on the pull-ups (R1/R2):** the GY-521 and GY-BMP280 modules already include their own onboard I2C pull-ups. The external resistors here sit in parallel with those, so the effective pull-up is stronger than ideal. On a future revision these would be raised in value or removed entirely. Left in place here as part of the learning exercise.

## Repository structure

```
.
├── README.md
├── *.PrjPcb              # Altium project file
├── *.SchDoc             # Schematic
├── *.PcbDoc             # PCB layout
├── *.SchLib / *.PcbLib  # Custom / imported libraries (header footprints)
├── gerbers/             # Fabrication outputs (Gerber + NC drill)
└── docs/                # Screenshots used in this README
```

## What I learned

- Representing breakout modules as headers rather than drawing the sensor ICs directly
- Importing and fixing third-party footprints (matching pad designators to symbol pins)
- Defining a shared I2C bus with net labels across multiple components
- Routing a two-layer board by hand, including layer changes via vias
- Running Design Rule Checks and interpreting violations
- Generating fabrication outputs (Gerbers + NC drill)

