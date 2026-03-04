# 🔌 CKS Professional ECU: Detailed Pin-to-Pin Connectivity Guide

This document is designed as a prompt for AI image generators (like ChatGPT/DALL-E) or as a manual wiring guide. It details every connection for the **CKS32F103 Professional ECU**.

---

## 🏗️ 1. Main Component Pinouts

### MCU: CKS32F103CBT6 (LQFP-48)
- **Power:** VDD (Pins 24, 36, 48) -> 3.3V; VSS (Pins 23, 35, 47) -> GND.
- **Clock:** OSC_IN (Pin 5) & OSC_OUT (Pin 6) -> 8MHz Crystal.
- **Inputs:** 
    - PA0 (Pin 10): Crank Signal (from MAX9926)
    - PA1 (Pin 11): Cam Signal (from MAX9926)
    - PA2 (Pin 12): TPS Sensor (ADC)
    - PA3 (Pin 13): MAP Sensor (ADC)
    - PA4 (Pin 14): CLT Sensor (ADC)
    - PA5 (Pin 15): IAT Sensor (ADC)
    - PA6 (Pin 16): O2 Sensor (ADC)
    - PB1 (Pin 19): Battery Voltage (via 10:1 Divider)
- **Outputs:**
    - PB6 (Pin 42): Injector 1
    - PB7 (Pin 43): Injector 2
    - PB8 (Pin 45): Injector 3
    - PB9 (Pin 46): Injector 4
    - PA8 (Pin 29): Ignition 1 (Cyl 1&4)
    - PA9 (Pin 30): Ignition 2 (Cyl 2&3) / Tuning TX
    - PB0 (Pin 18): Boost Control PWM
    - PB12 (Pin 25): Fuel Pump Relay
    - PB13 (Pin 26): Cooling Fan Relay
- **Communication:**
    - PA10 (Pin 31): Tuning RX
    - PB10 (Pin 21): Bluetooth TX
    - PB11 (Pin 22): Bluetooth RX

---

## ⚡ 2. Power Section Wiring
1.  **12V Battery Input** -> 15A Fuse -> Anode of 1N5408 Diode.
2.  **1N5408 Cathode** -> **12V_Protected Rail**.
3.  **12V_Protected Rail** -> Pin 1 of LM2940 (5V Reg) AND Cathode of 5KP15A TVS Diode.
4.  **5KP15A Anode** -> GND.
5.  **LM2940 Output (Pin 3)** -> **5V_Rail** (Powers sensors and 3.3V Reg).
6.  **5V_Rail** -> Pin 3 of AMS1117 (3.3V Reg).
7.  **AMS1117 Output (Pin 2)** -> **3.3V_Rail** (Powers MCU and Bluetooth).

---

## 🛰️ 3. Sensor Input Protection (For Each Analog Pin)
Each sensor (TPS, MAP, CLT, IAT, O2) follows this path:
1.  **Sensor Signal** -> 1kΩ Resistor -> **Filtered Node**.
2.  **Filtered Node** -> 100nF Capacitor -> GND.
3.  **Filtered Node** -> 3.3V Zener Diode (Cathode) -> GND (Anode).
4.  **Filtered Node** -> **Corresponding MCU Pin**.

---

## ⚙️ 4. Actuator Driver Wiring

### Injector Drivers (4 Channels)
- **MCU Pin (e.g., PB6)** -> 100Ω Resistor -> **Gate of IRLZ44N**.
- **Gate of IRLZ44N** -> 10kΩ Resistor -> GND.
- **Source of IRLZ44N** -> GND.
- **Drain of IRLZ44N** -> **Injector Connector Pin**.

### Ignition Drivers (2 Channels)
- **MCU Pin (e.g., PA8)** -> 220Ω Resistor -> **Gate of ISL9V5036P3 IGBT**.
- **Emitter of IGBT** -> GND.
- **Collector of IGBT** -> **Ignition Coil Connector Pin**.

---

## 🔌 5. External Connector (Modern Vehicle Interface)
*Mapping to the 18 main pins:*
- **Pin 1-2:** 12V_Battery (Power Input)
- **Pin 3:** 12V_Switched (Ignition Key)
- **Pin 4-6:** Power Ground (Engine Block)
- **Pin 7-9:** Sensor Ground (Isolated)
- **Pin 10:** Ignition 1 Output
- **Pin 11:** Ignition 2 Output
- **Pin 12-15:** Injectors 1-4 Outputs
- **Pin 16-17:** Crank Signal Positive/Negative
- **Pin 18:** Turbo Boost Solenoid Output
