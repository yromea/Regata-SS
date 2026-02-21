# ⚙️ Classic Carb & EFI Safety ECU (Egypt-Sourced Components)

[![MCU](https://img.shields.io/badge/MCU-STM32F103-blue)](#-mcu-variants)
[![Status](https://img.shields.io/badge/status-early%20development-orange)](#-project-status)
[![License](https://img.shields.io/badge/license-open-lightgrey)](#-license)

An open ECU platform designed to make classic carbureted and EFI engines **safer, smarter, and more reliable**, using components that can be sourced from the **Egyptian electronics market**.

This project started from a real and dangerous incident: a classic carbureted car caught fire on a desert highway between Cairo and Alexandria. By the goodness and mercy of God, the driver survived. This ECU project is a direct response to that experience, aiming to give classic cars modern levels of **fuel control, ignition control, and safety monitoring**.

---

## 📚 Table of Contents

- [⚙️ Classic Carb & EFI Safety ECU (Egypt-Sourced Components)](#️-classic-carb--efi-safety-ecu-egypt-sourced-components)
  - [📚 Table of Contents](#-table-of-contents)
  - [🚗 Project Overview](#-project-overview)
  - [🧠 MCU Variants](#-mcu-variants)
  - [📄 Documentation Chapters](#-documentation-chapters)
    - [Intro & Story](#intro--story)
    - [Chapter 1 – Egypt Market Research](#chapter-1--egypt-market-research)
    - [Chapter 2 – CKS32F103C8T6 ECU Pinout](#chapter-2--cks32f103c8t6-ecu-pinout)
    - [Chapter 2R – STM32F103RCT6 ECU Pinout](#chapter-2r--stm32f103rct6-ecu-pinout)
  - [🛠 Planned Next Chapters](#-planned-next-chapters)
  - [🤝 Acknowledgements](#-acknowledgements)
  - [📦 Project Status](#-project-status)
  - [📜 License](#-license)

---

## 🚗 Project Overview

- **Goal**: Provide an open, affordable ECU platform that can:
  - Support **pure carb** engines with electronic ignition and safety logic
  - Support **hybrid / half injection** setups (e.g. throttle body injection)
  - Support **full EFI** (multi-point injection) for 4-cylinder engines
- **Target vehicle to start with**:
  - Fiat Regata 85S (with air conditioning), originally **full carb**
  - This car will be converted to **full injection** using this ECU
- **Target users**:
  - Owners of classic carbureted cars in Egypt and similar markets
  - Electronics enthusiasts and workshops who want a reproducible, documented solution
- **Key priorities**:
  - Safety: controlled fuel pump, injector shutoff, proper fusing, monitored conditions
  - Reliability: stable idle, better cold start, smarter ignition
  - Diagnostics: TunerStudio-style tuning and logging, OBD-II style connector in the cabin

Author and contact:

- **Eng. Ebram Dawood**
- Phone: `+2 01558395837`

---

## 🧠 MCU Variants

This project deliberately supports **two MCU options**, so builders can choose based on availability and required features.

- **CKS32F103C8T6**
  - 48-pin, Cortex-M3 clone of STM32F103C8T6
  - Approx. 64 KB flash, 20 KB RAM
  - Ideal for a **compact 4-cylinder ECU** with:
    - 4 injectors
    - 2 wasted-spark ignition outputs
    - Standard sensors and safety/aux outputs

- **STM32F103RCT6**
  - 64-pin genuine STM32F103, larger package
  - Approx. 256 KB flash, 48 KB RAM
  - Better for an **expanded ECU** with:
    - Extra sensors (oil temp/pressure, fuel pressure, boost)
    - More relays and actuators
    - More complex firmware and diagnostics

The documentation contains **parallel pinout chapters** for both MCUs so the same project can scale from a compact ECU to a more advanced one.

---

## 📄 Documentation Chapters

All design documents are kept as Markdown files in the repository for easy reading and version control.

### Intro & Story

- **File**: [`intro.md`](./intro.md)
- Contains:
  - Project vision and safety motivation
  - The highway fire incident that inspired this work
  - Details about the author and the first target vehicle (Fiat Regata 85S)

### Chapter 1 – Egypt Market Research

- **File**: [`chapter1.md`](./chapter1.md)
- Focus:
  - Survey of components realistically available in Egypt:
    - MCUs and dev boards (Atmega2560, STM32F103, etc.)
    - Power and protection components
    - Sensor interface components
    - Injector and ignition drivers
    - Communication interfaces (USB-UART, CAN, OBD-II connector)
  - Strategy for choosing parts that local electronics stores keep in stock

### Chapter 2 – CKS32F103C8T6 ECU Pinout

- **File**: [`chapter2.md`](./chapter2.md)
- Section **2.A** in this file describes:
  - Full pin assignment for **CKS32F103C8T6** (48-pin)
  - Mapping for:
    - Crank and cam inputs
    - 4 injector outputs
    - 2 ignition outputs (wasted spark)
    - TPS, MAP, CLT, IAT, O2, battery sense
    - Fuel pump, fan, idle valve, spare outputs
    - Tuning UART and reserved CAN pins
  - Designed for:
    - 4-cylinder full EFI
    - Hybrid / half injection
    - Pure carb with electronic ignition and safety

### Chapter 2R – STM32F103RCT6 ECU Pinout

- **File**: [`chapter2R.md`](./chapter2R.md)
- Focus:
  - Dedicated pinout for **STM32F103RCT6** (64-pin, larger flash/RAM)
  - Keeps the same core mapping as the CKS32F103C8T6 version for:
    - Crank/cam, injectors, ignition, key sensors, pump, fan, idle, tuning UART, CAN, status LED
  - Defines extra pins for:
    - Oil temperature, oil pressure, fuel or boost pressure
    - Additional relays and actuators
    - Future expansion (VVT, boost control, external modules)

---

## 🛠 Planned Next Chapters

Upcoming documentation (files to be added as the project progresses):

- **Chapter 3 – Hardware & Schematics**
  - Crank/cam interface (VR/Hall conditioning)
  - Injector and ignition driver stages
  - Power supply and protection (12 V → 5 V/3.3 V)

- **Chapter 4 – Firmware Architecture**
  - Real-time engine loop
  - Table storage and configuration
  - TunerStudio protocol implementation

- **Chapter 5 – Tuning & OBD-II Integration**
  - Base maps for the Fiat Regata 85S
  - Logging, diagnostics, and OBD-II style connector usage

---

## 🤝 Acknowledgements

This project exists thanks to:

- **God’s protection and goodness**, turning a near-fatal fire into a motivation to help others drive safer cars.
- **My wife, Sarah Akram**, for her constant support, patience, and cheering throughout this project. Her encouragement is a huge part of why this work continues even when it is technically and emotionally challenging.

---

## 📦 Project Status

Early design phase:

- ✅ Vision, story, and goals documented (`intro.md`)
- ✅ Egypt market research for components (`chapter1.md`)
- ✅ Dual pinout chapters for:
  - CKS32F103C8T6 (`chapter2.md`, section 2.A)
  - STM32F103RCT6 (`chapter2R.md`)
- ⏳ Hardware schematics, PCB layouts, and firmware: **in progress**

Contributions, feedback, and local Egyptian sourcing tips are very welcome.

---

## 📜 License

The project is intended to be **open hardware and open firmware**.  
License details will be added once the first hardware and firmware releases are prepared. Until then, consider the repository as shared for **learning, discussion, and early collaboration**.

