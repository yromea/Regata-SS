# Bill of Materials (BOM) - Standalone Custom PCB (Bare MCU)

This BOM is for building a fully custom ECU from scratch, soldering the **CKS32F103C8T6 (or STM32F103RCT6)** chip directly onto your CNC-milled PCB.

**Warning:** This requires soldering a fine-pitch LQFP-48 or LQFP-64 chip (0.5mm pin pitch). Ensure your CNC mill can produce traces this fine (0.2mm - 0.3mm width).

---

## 1. Microcontroller & Essential Support
| Qty | Component | Value/Part | Package | Note |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **MCU** | **CKS32F103C8T6** | LQFP-48 | The brain. (Or RCT6 if you chose that). |
| 1 | **Crystal** | **8 MHz** | HC-49S (Low Profile) | Main system clock. |
| 2 | **Capacitor** | **22pF** | 0805 or Ceramic Disc | Load caps for 8MHz crystal. |
| 1 | **Resistor** | **10kΩ** | 0805 / 1/4W | Reset Pull-up (NRST pin). |
| 1 | **Button** | Tactile Switch | 6x6mm THT | Reset button. |
| 1 | **Capacitor** | **100nF** | 0805 / Ceramic | Decoupling for NRST. |
| 1 | **Header** | 4-pin Male | 2.54mm | **SWD Programming Port** (GND, CLK, DIO, 3.3V). |
| 2 | **Resistor** | **10kΩ** | 0805 / 1/4W | BOOT0 / BOOT1 pull-down resistors. |

## 2. Power Regulation (12V -> 5V -> 3.3V)
*Since we are bare metal, we need to generate 3.3V for the MCU ourselves.*
| Qty | Component | Value/Part | Package | Note |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **Regulator** | **LM7805** | TO-220 | 12V -> 5V (Main 5V rail). |
| 1 | **Regulator** | **AMS1117-3.3** | SOT-223 | 5V -> 3.3V (MCU power). |
| 2 | **Capacitor** | **100nF** | 0805 / Ceramic | Input/Output filter for AMS1117. |
| 2 | **Capacitor** | **10µF** | Tantalum or Electrolytic | Bulk capacitance for 3.3V rail. |
| 1 | **Diode** | **1N5408** | Axial | Reverse Polarity Protection. |
| 1 | **TVS Diode** | **5KP15A** | Axial | Surge Protection (Load dump). |

## 3. Communication (USB-UART)
*To talk to TunerStudio. You can use an external USB-TTL cable, or build it in.*
| Qty | Component | Value/Part | Package | Note |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **Header** | 4-pin Male | 2.54mm | UART Header (TX, RX, GND, 5V). |
| *Opt* | *CH340G* | *USB-Serial* | *SOP-16* | *Optional: On-board USB (requires 12MHz crystal).* |

## 4. Injector & Ignition Drivers (Same as Trial 1)
| Qty | Component | Value/Part | Package | Note |
| :--- | :--- | :--- | :--- | :--- |
| 2-4 | **MOSFET** | **IRLZ44N** | TO-220 | Injector Drivers. |
| 2-4 | **Resistor** | **100Ω** | 1/4W | Gate Resistor. |
| 2-4 | **Resistor** | **10kΩ** | 1/4W | Gate Pull-down. |
| 2 | **IGBT** | **ISL9V5036P3** | TO-220 | Ignition Drivers (if using dumb coils). |

## 5. Sensor Inputs (Same as Trial 1)
| Qty | Component | Value/Part | Package | Note |
| :--- | :--- | :--- | :--- | :--- |
| 4-6 | **Resistor** | **1kΩ** | 1/4W | Input Series Resistor. |
| 4-6 | **Capacitor** | **100nF** | Ceramic | Low Pass Filter. |
| 4-6 | **Zener** | **3.3V** | Axial | **CRITICAL:** Protects bare MCU pins. |

---

## PCB Manufacturing Notes (CNC)
- **Trace Width:**
  - **MCU Fanout:** 0.25mm (Very challenging on CNC!).
  - **Power:** 1.0mm - 1.5mm.
  - **Signals:** 0.4mm - 0.6mm.
- **Vias:** Minimize vias! Single-sided design is preferred. If double-sided, you must solder wire through holes manually.
- **Clearance:** 0.2mm minimum for LQFP pads.
