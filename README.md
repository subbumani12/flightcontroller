# Flight Controller

A custom **STM32F411-based flight controller PCB** designed for multirotor, UAV, robotics, and embedded flight-control applications.

The board integrates an IMU, magnetometer, barometric sensor, EEPROM, MicroSD storage, USB-C, SWD debugging, UART interfaces, and dedicated motor/auxiliary outputs on a compact **2-layer PCB** designed in KiCad.

## 3D Render
<p align="center">
  <img src="fc_3d.png" alt="STM32 Flight Controller 3D Render" width="700">
</p>

---

## ✈️ Overview

This project is a custom flight-controller hardware design centered around the **STM32F411RET6 ARM Cortex-M4** microcontroller.

The STM32 handles sensor acquisition, flight-control processing, peripheral communication, data logging, and motor/auxiliary output generation.

The board combines:

* Inertial sensing
* Magnetic heading measurement
* Barometric altitude measurement
* Non-volatile configuration storage
* Flight-data logging
* USB communication
* SWD programming/debugging
* Motor and auxiliary outputs
* UART communication

The PCB was designed in **KiCad**, with emphasis on compact routing, sensor decoupling, accessible debugging, and organized external interfaces.

---

## 🔧 Hardware Features

| Component             | Part                                              |
| --------------------- | ------------------------------------------------- |
| **Microcontroller**   | STM32F411RET6                                     |
| **IMU**               | LSM6DS3 – 3-axis Accelerometer + 3-axis Gyroscope |
| **Magnetometer**      | LIS3MDL – 3-axis                                  |
| **Barometer**         | BMP280                                            |
| **EEPROM**            | AT24C256A                                         |
| **Storage**           | MicroSD                                           |
| **USB**               | USB Type-C                                        |
| **Debug**             | SWD                                               |
| **Serial**            | UART1 / UART2                                     |
| **Motor Outputs**     | MOTOR1 / MOTOR2 / MOTOR3                          |
| **Auxiliary Outputs** | AUX1 / AUX2 / AUX3                                |
| **Logic Supply**      | 3.3 V                                             |
| **PCB**               | 2-layer                                           |
| **EDA Tool**          | KiCad                                             |

The complete hardware feature set is documented in the original design specification.

---

## 🧠 System Architecture

```text
                         USB Type-C
                             │
                             ▼
                       USB Protection
                             │
                             ▼
                       Power / 3.3 V
                             │
                             ▼
                    ┌─────────────────┐
                    │   STM32F411     │
                    │   Flight MCU    │
                    └────────┬────────┘
                             │
          ┌──────────────────┼──────────────────┐
          │                  │                  │
          ▼                  ▼                  ▼
        I²C                 SPI               UART
          │                  │                  │
    ┌─────┼─────┐       ┌────┴────┐       ┌────┴────┐
    ▼     ▼     ▼       ▼         ▼       ▼         ▼
  LSM6DS3 LIS3MDL EEPROM BMP280   MicroSD  UART1    UART2
    │     │
    └─────┴────── Sensor Data
                    │
                    ▼
              STM32 Processing
                    │
             ┌──────┴──────┐
             ▼             ▼
        Motor Outputs   AUX Outputs
             │
             ▼
            ESCs
```

---

## 📡 Sensors & Peripherals

### LSM6DS3 IMU

The **LSM6DS3** provides:

* 3-axis accelerometer
* 3-axis gyroscope

It serves as the primary inertial sensor for attitude estimation, angular-rate measurement, motion detection, and flight-control feedback.

The IMU communicates with the STM32 through **I²C**.

### LIS3MDL Magnetometer

The **LIS3MDL** provides 3-axis magnetic-field measurements and can be used as a magnetic heading reference for navigation and orientation estimation.

It communicates through **I²C** and includes an interrupt connection to the MCU.

### BMP280 Barometer

The **BMP280** provides pressure measurements for:

* Relative altitude estimation
* Altitude-hold applications
* Vertical-motion estimation
* Flight-data logging

The sensor is connected through **SPI**.

### AT24C256A EEPROM

The onboard EEPROM provides non-volatile storage for:

* Configuration parameters
* Calibration data
* Controller settings
* Persistent system information

### MicroSD

A dedicated MicroSD interface provides onboard flight-data storage using SPI:

```text
SD_SPI_MOSI
SD_SPI_MISO
SD_SPI_SCK
SD_CHIPSEL
```

It can be used for sensor measurements, telemetry, debugging information, controller parameters, and flight logs.

---

## 🔌 Interfaces

### Motor Outputs

Three dedicated motor interfaces are provided:

```text
MOTOR1_OUT
MOTOR2_OUT
MOTOR3_OUT
```

Each connector provides:

```text
+5V
GND
SIGNAL
```

The motor-control signals are generated using STM32 timer peripherals and can interface with external ESCs or motor-control hardware.

### Auxiliary Outputs

```text
AUX1_OUT
AUX2_OUT
AUX3_OUT
```

Each provides:

```text
+5V
GND
SIGNAL
```

These can be used for additional actuators or flight-controller peripherals.

### UART

Two serial interfaces are exposed:

```text
UART1_TX
UART1_RX

UART2_TX
UART2_RX
```

Potential applications include GPS, telemetry, radio receivers, external sensors, and configuration interfaces.

### SWD

A dedicated SWD interface is provided for programming and debugging:

```text
SWDIO
SWCLK
3.3V
GND
```

The board also includes test points for convenient hardware debugging.

---

## ⚡ Power Architecture

The board uses an onboard **3.3 V regulation stage** for the STM32 and peripheral electronics.

The power section includes:

* Schottky diode protection
* AMS1117-3.3 regulator
* Input/output filtering capacitors
* Power indicator LED
* Ferrite filtering for the MCU supply

The additional ferrite filtering is intended to reduce high-frequency supply noise around the MCU.

---

## 🖥️ USB & Debugging

The board includes a **USB Type-C interface** providing:

* USB D+
* USB D−
* VBUS
* GND

USB ESD protection is included on the data interface.

The USB connection can be used for firmware development, communication, configuration, debugging, and powering the board during development.

Dedicated **BOOT** and **RESET** buttons are also provided for firmware programming, recovery, and MCU reset.

---

## 🧩 PCB Design

Designed using **KiCad**, the PCB focuses on compact two-layer implementation and practical hardware debugging.

### Design considerations

* Compact 2-layer PCB
* Dedicated ground routing/plane
* Local sensor decoupling
* Short high-speed peripheral connections
* Compact MCU placement
* Accessible SWD interface
* Dedicated motor and auxiliary connectors
* UART connectors
* USB Type-C
* Ferrite-filtered MCU supply
* Separated sensitive sensor circuitry
* Hardware test points
* Perimeter mounting holes

---

## 🔄 Flight-Control Data Flow

```text
        ┌─────────────┐
        │    IMU      │
        │   LSM6DS3   │
        └──────┬──────┘
               │
        ┌──────▼──────┐
        │ Magnetometer│
        │   LIS3MDL   │
        └──────┬──────┘
               │
        ┌──────▼──────┐
        │  Barometer  │
        │    BMP280   │
        └──────┬──────┘
               │
               ▼
        ┌──────────────┐
        │  STM32F411   │
        │    MCU       │
        └──────┬───────┘
               │
        ┌──────┼──────────┐
        ▼      ▼          ▼
     Motors   AUX     Telemetry
        │
        ▼
       ESCs
```

The STM32 acquires sensor data, processes the measurements using flight-control firmware, and generates outputs for connected ESCs, motors, and auxiliary peripherals.

---

## 🛠️ Tools & Technologies

* **KiCad** – Schematic & PCB design
* **STM32F411RET6** – ARM Cortex-M4 MCU
* **STM32 development tools / STM32CubeIDE**
* **I²C**
* **SPI**
* **USART**
* **USB**
* **SWD**

---

## 🚀 Potential Future Improvements

Possible future revisions include:

* Additional motor channels
* CAN / CAN-FD
* GPS connector
* Dedicated telemetry interface
* Battery voltage/current monitoring
* Buzzer interface
* Additional IMU / redundant sensors
* External compass connector
* Barometer isolation
* Improved power-input protection
* Dedicated receiver interface
* Additional test points
* Improved PCB signal integrity

---

## 🎯 Applications

This hardware platform can be explored for:

* Multirotor flight controllers
* UAV development
* Drone flight-control systems
* Autonomous aerial vehicles
* Embedded navigation
* Sensor-fusion experiments
* Flight-data logging
* Robotics
* Embedded control systems
* UAV hardware prototyping

---

## 📌 Project Status

**Hardware:** PCB design completed
**EDA:** KiCad
**Architecture:** STM32F411-based
**PCB:** 2-layer

This project is intended for **educational, research, and UAV hardware-development purposes**.

---

## 📄 License

This project is open-source and intended for educational, research, and UAV hardware-development purposes.

