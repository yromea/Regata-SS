# 🛒 Master Shopping List for Regata SS ECU

This document contains the shopping lists for all **3 ECU Versions**.
To build an ECU, you need to buy:
1.  **The "Common Parts"** (Used in all versions).
2.  **The "Version Specific Parts"** (Only for the version you chose).

---

## 1. Common Parts (Buy these for ANY version)
*These components handle power, sensors, and actuating the engine. They are the same regardless of which MCU you use.*

### A. Power Supply (12V → 5V)
| Qty | Component | Value/Part | Description |
| :--- | :--- | :--- | :--- |
| 1 | **Regulator** | **LM7805** | Main 5V regulator (TO-220). |
| 1 | **Diode** | **1N5408** | Reverse polarity protection (3A). |
| 1 | **TVS Diode** | **5KP15A** or P6KE22A | Surge protection (critical for car voltage spikes). |
| 1 | **Capacitor** | **470µF / 25V** | Input bulk filter (Electrolytic). |
| 1 | **Capacitor** | **100µF / 16V** | Output bulk filter (Electrolytic). |
| 2 | **Capacitor** | **100nF (104)** | Noise filtering (Ceramic). |

### B. Injector Drivers (For 4 Cylinders)
| Qty | Component | Value/Part | Description |
| :--- | :--- | :--- | :--- |
| 4 | **MOSFET** | **IRLZ44N** | **MUST be Logic Level** (opens fully at 3.3V/5V). |
| 4 | **Resistor** | **100Ω** | Gate resistor (1/4W). |
| 4 | **Resistor** | **10kΩ** | Gate pull-down resistor (1/4W). |
| 4 | **Diode** | **1N4007** or 1N5819 | Flyback protection (across injector). |

### C. Ignition Drivers (For Wasted Spark)
| Qty | Component | Value/Part | Description |
| :--- | :--- | :--- | :--- |
| 2 | **Resistor** | **220Ω** | Output protection resistor (1/4W). |
| *2* | *IGBT* | *ISL9V5036P3* | *(Only needed if using "Dumb Coils" without built-in igniters).* |

### D. Sensor Inputs (Protection & Filters)
| Qty | Component | Value/Part | Description |
| :--- | :--- | :--- | :--- |
| 6 | **Resistor** | **1kΩ** | Input series protection. |
| 6 | **Capacitor** | **100nF (104)** | Low-pass noise filter. |
| 6 | **Zener** | **3.3V / 0.5W** | **CRITICAL:** Protects MCU pins from >3.3V. |
| 2 | **Resistor** | **2.49kΩ** | Bias pull-up for CLT and IAT sensors (1% tolerance preferred). |

### E. Connectors
| Qty | Component | Type | Description |
| :--- | :--- | :--- | :--- |
| 1 | **Screw Terminal** | 2-pin (5mm) | Main Power (12V, GND). |
| 1 | **Screw Terminal** | 4-pin (5mm) | Injectors. |
| 1 | **Screw Terminal** | 3-pin (5mm) | Ignition Coil. |
| 1 | **Pin Header** | Male Strip (1x40) | For sensors and extras. |

---

## 2. Version Specific Parts (Choose ONE path)

### 🔵 Path 1: The "Blue Pill" Shield (Easiest Start)
*Best for beginners. You buy a ready-made STM32 module and plug it into your PCB.*

| Qty | Component | Description |
| :--- | :--- | :--- |
| 1 | **CKS32F103C8T6** Board | "Blue Pill" Development Board (STM32F103 clone). |
| 2 | **Female Header** | 1x20 (2.54mm) to make a socket for the Blue Pill. |
| 1 | **USB-TTL Adapter** | FTDI or CP2102 (for tuning via laptop). |

### 🔴 Path 2: The "Bare Metal" Pro (Advanced)
*Best for professional custom PCBs. You solder the MCU chip directly.*

| Qty | Component | Description |
| :--- | :--- | :--- |
| 1 | **MCU Chip** | **STM32F103RCT6** (64-pin) OR **CKS32F103C8T6** (48-pin). |
| 1 | **Regulator** | **AMS1117-3.3** (SOT-223) to create 3.3V from 5V. |
| 2 | **Capacitor** | **10µF** (Tantalum/Electrolytic) for 3.3V rail. |
| 1 | **Crystal** | **8 MHz** (HC-49S Low Profile). |
| 2 | **Capacitor** | **22pF** (Ceramic) for Crystal. |
| 1 | **Button** | Tactile Switch (6x6mm) for Reset. |
| 1 | **Resistor** | **10kΩ** (Reset Pull-up). |
| 1 | **Header** | 1x4 Male (for ST-Link SWD programming). |

### 🟢 Path 3: The "Pico Power" (CNC Friendly)
*Best for home CNC milling. Powerful and easy to mount.*

| Qty | Component | Description |
| :--- | :--- | :--- |
| 1 | **Raspberry Pi Pico** | RP2040 Board (Ensure it has castellated edges). |
| 1 | **Multiplexer** | **CD4051** (DIP-16) or 74HC4051. |
| 1 | **USB Cable** | Micro-USB (for programming & tuning). |
| *Opt* | *Voltage Ref* | *TL431 (Optional, for cleaner sensor readings).* |

---

## 3. CNC PCB Manufacturing Supplies
*If you are milling the PCB yourself on the CNC 3018 Pro.*

| Item | Description |
| :--- | :--- |
| **Copper Clad Board** | FR4, Single-sided (easier) or Double-sided, 70x100mm or 100x150mm. |
| **V-Bit** | 10°, 20°, or 30° V-bit (0.1mm tip) for isolation routing. |
| **Drill Bits** | 0.8mm (components), 1.0mm (headers), 3.0mm (mounting holes). |
| **Sandpaper** | 600-1000 grit for cleaning the copper before soldering. |
