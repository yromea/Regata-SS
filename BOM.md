# Bill of Materials (BOM) - Trial 1 PCB (CNC 3018 Pro)

This BOM is designed for a **CNC-milled PCB** (single-sided or simple double-sided).
Components are chosen for easy soldering and availability in Egyptian electronics stores (Ram, Future, Free, etc.).

**Note:** For "Trial 1", we are building a **Breakout / Shield Board** for the STM32 "Blue Pill" module. We are *not* soldering the MCU chip directly to the copper yet, as fine-pitch SMD is hard for a first CNC milling project.

---

## 1. Core Module
| Qty | Component | Description | Local Store Note |
| :--- | :--- | :--- | :--- |
| 1 | **STM32F103C8T6 / CKS32F103C8T6** | "Blue Pill" Development Board | Standard module, widely available. |
| 2 | **40-pin Female Header (2.54mm)** | Cut to 2x20 pins | To socket the Blue Pill. |

## 2. Power Supply (12V -> 5V)
| Qty | Component | Value | Package | Description |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **LM7805** | 5V Regulator | TO-220 | Main 5V supply for Blue Pill & sensors. |
| 1 | **Diode** | 1N5408 / 1N4007 | Axial (THT) | Reverse polarity protection. |
| 1 | **Capacitor** | 470µF / 25V | Radial (THT) | Bulk input filtering. |
| 1 | **Capacitor** | 100µF / 16V | Radial (THT) | Output filtering (5V rail). |
| 2 | **Capacitor** | 100nF (104) | Ceramic (THT) | High-freq noise filtering. |

## 3. Injector Drivers (2 Channels)
| Qty | Component | Value | Package | Description |
| :--- | :--- | :--- | :--- | :--- |
| 2 | **MOSFET** | **IRLZ44N** | TO-220 | Logic-level N-channel MOSFET. |
| 2 | **Resistor** | 100Ω | 1/4W (THT) | Gate resistor. |
| 2 | **Resistor** | 10kΩ | 1/4W (THT) | Gate pull-down resistor. |
| 2 | **Diode** | 1N4007 / 1N5819 | Axial (THT) | Flyback clamping (optional if MOSFET has robust avalanche, but recommended). |

## 4. Ignition Drivers (2 Channels - Logic Level)
*For driving Smart Coils (built-in igniter) or external igniter module.*
| Qty | Component | Value | Package | Description |
| :--- | :--- | :--- | :--- | :--- |
| 2 | **Resistor** | 220Ω | 1/4W (THT) | Series protection resistor. |
| *Opt* | *TC4427* | *Gate Driver* | *DIP-8* | *Optional: If driving dumb coils with big IGBTs later.* |

## 5. Sensor Inputs (4 Channels: TPS, MAP, CLT, IAT)
| Qty | Component | Value | Package | Description |
| :--- | :--- | :--- | :--- | :--- |
| 4 | **Resistor** | 1kΩ | 1/4W (THT) | Series input protection. |
| 4 | **Capacitor** | 100nF (104) | Ceramic (THT) | Low-pass filter (noise reduction). |
| 4 | **Zener Diode** | 3.3V / 0.5W | Axial (THT) | Over-voltage protection (Critical for STM32 inputs!). |
| 2 | **Resistor** | 2.49kΩ (or 2.2kΩ) | 1/4W (THT) | Pull-up bias resistor for CLT & IAT (5V rail). |

## 6. Crank Sensor Input (VR Conditioner or Opto)
*Simple minimal circuit for testing RPM (Square wave / Hall).*
| Qty | Component | Value | Package | Description |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **Optocoupler** | 4N25 / PC817 | DIP-4/6 | Isolation for RPM signal (safest for beginner). |
| 1 | **Resistor** | 1kΩ | 1/4W (THT) | LED current limit. |
| 1 | **Resistor** | 4.7kΩ | 1/4W (THT) | Pull-up on output side. |

## 7. Connectors
| Qty | Component | Details | Package | Description |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **Screw Terminal** | 2-pin (5mm) | THT | 12V Power Input. |
| 1 | **Screw Terminal** | 4-pin (5mm) | THT | Injector Outputs. |
| 1 | **Screw Terminal** | 3-pin (5mm) | THT | Ignition Outputs. |
| 1 | **Pin Header** | 1x40 Male | 2.54mm | For sensor connections (dupont) or screw terminals. |

---

## Summary for CNC Milling
- **Total Copper Area:** Approx 100mm x 70mm (Standard copper clad board).
- **Drill Bits Needed:** 0.8mm (resistors/caps), 1.0mm (headers/diodes), 1.2mm (terminals/TO-220).
- **V-Bit:** 20-30 degree V-bit for isolation milling.
