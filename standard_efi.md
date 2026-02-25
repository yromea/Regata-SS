# Standard EFI Configuration (Core Requirements)

## 1. Overview

This document defines the **"Standard EFI"** specification for this project.
The goal is to provide a reliable, minimal-fuss configuration that covers 90% of classic 4-cylinder engine conversions (like the Fiat Regata 85S) without unnecessary complexity.

It focuses on:
- **Reliability & Safety:** Essential engine control functions only.
- **Simplicity:** No extra dashboard sensors (oil/water/fuel level) for now.
- **Compatibility:** Works on both **CKS32F103C8T6** (48-pin) and **STM32F103RCT6** (64-pin).

---

## 2. Core Features

### 2.1 Engine Configuration
- **Cylinders:** 4 (Inline)
- **Injection Mode:**
  - **Semi-Sequential / Batch:** 2 channels (pairing cylinders 1&4, 2&3).
  - **Full Sequential (Optional):** 4 channels (if MCU pins allow).
- **Ignition Mode:**
  - **Wasted Spark:** 2 channels (firing coils for 1&4, 2&3).
  - **Distributor:** 1 channel (single coil).

### 2.2 Essential Sensors (Inputs)
These are the **mandatory** inputs for a running EFI system:
1.  **Crank Position (RPM):** VR or Hall sensor.
    - *Critical for timing.*
2.  **MAP (Manifold Absolute Pressure):** 0-5V signal.
    - *Primary load sensing (Speed-Density).*
3.  **CLT (Coolant Temperature):** Thermistor (NTC).
    - *Warm-up enrichment & fan control.*
4.  **IAT (Intake Air Temperature):** Thermistor (NTC).
    - *Air density correction.*
5.  **TPS (Throttle Position):** 0-5V potentiometer.
    - *Acceleration enrichment & flood clear.*
6.  **O2 / Lambda (AFR):** Narrowband or Wideband controller (0-5V).
    - *Closed-loop fueling.*
7.  **Battery Voltage:**
    - *Injector dead-time compensation.*

### 2.3 Essential Actuators (Outputs)
These are the **mandatory** outputs to run the engine:
1.  **Injectors:** 2 or 4 channels (High-Z).
2.  **Ignition Coils:** 1 or 2 channels (Logic-level or High-current).
3.  **Fuel Pump Relay:** Safety cutoff logic.
4.  **Idle Valve:** PWM (2-wire) or Stepper (4-wire).
5.  **Cooling Fan Relay:** On/Off based on CLT.

---

## 3. Pinout Mapping (Universal)

This mapping works for **both** CKS32F103C8T6 and STM32F103RCT6.
It uses the common pins available on the standard "Blue Pill" footprint.

| Function | Pin Name | Description |
| :--- | :--- | :--- |
| **Crank Signal** | **PA0** | TIM2_CH1 (Input Capture) |
| **Cam Signal** | **PA1** | TIM2_CH2 (Optional for sequential) |
| **TPS Sensor** | **PA2** | ADC1_IN2 |
| **MAP Sensor** | **PA3** | ADC1_IN3 |
| **CLT Sensor** | **PA4** | ADC1_IN4 |
| **IAT Sensor** | **PA5** | ADC1_IN5 |
| **Idle Valve** | **PA6** | TIM3_CH1 (PWM) |
| **Ignition 1** | **PA8** | TIM1_CH1 (Cyl 1&4) |
| **Ignition 2** | **PA9** | TIM1_CH2 (Cyl 2&3) |
| **O2 Sensor** | **PB0** | ADC1_IN8 |
| **Battery V** | **PB1** | ADC1_IN9 |
| **Injector 1** | **PB6** | TIM4_CH1 (Cyl 1 or 1&4) |
| **Injector 2** | **PB7** | TIM4_CH2 (Cyl 2 or 2&3) |
| **Injector 3** | **PB8** | TIM4_CH3 (Cyl 3, optional) |
| **Injector 4** | **PB9** | TIM4_CH4 (Cyl 4, optional) |
| **Tuning TX** | **PB10** | UART3 TX (to PC) |
| **Tuning RX** | **PB11** | UART3 RX (from PC) |
| **Fuel Pump** | **PB12** | Digital Output (Relay) |
| **Fan Relay** | **PB13** | Digital Output (Relay) |

---

## 4. Hardware Requirements (Egypt Market)

### 4.1 Power Supply
- **Input:** 12V - 14.5V Automotive.
- **Protection:** TVS Diode (e.g., 5KP15A or P6KE22A), Reverse polarity diode (1N5408 or Schottky).
- **Regulation:** 5V Linear (7805 or LM2940) + 3.3V LDO (AMS1117-3.3).

### 4.2 Injector Drivers
- **Component:** Low-side MOSFET.
- **Recommendation:** **IRLZ44N** or **STP55NF06** (Logic Level).
- **Circuit:** Gate resistor (100Ω) + Pull-down (10kΩ) + Flyback clamping.

### 4.3 Ignition Drivers
- **Option A (Smart Coils - Built-in igniter):**
  - Direct 5V/3.3V logic drive (with small buffer like TC4427 or simple NPN transistor).
- **Option B (Dumb Coils - No igniter):**
  - **IGBT:** **ISL9V5036P3** or **GB14C40L** (specifically designed for ignition).
  - *Note: Do not use standard MOSFETs for ignition coils due to high voltage spikes.*

### 4.4 Sensor Inputs
- **Protection:** RC Low-pass filter (e.g., 1kΩ + 100nF) + 3.3V Zener diode on all ADC inputs.
- **Bias:** 2.49kΩ pull-up resistor to 5V for Thermistors (CLT/IAT).

---

## 5. Summary

This "Standard EFI" configuration provides:
- **Full Fuel & Spark Control** for your Regata 85S.
- **Safety Features** (Pump, Fan, Rev Limit).
- **Tuning Capability** via TunerStudio.
- **Upgrade Path:** You can add the extra sensors later without changing the core ECU logic, just by utilizing spare pins on the RCT6 if you chose that route.
