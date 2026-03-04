# Raspberry Pi Pico (RP2040) ECU Design

This document details how to use the **Raspberry Pi Pico (RP2040)** as the core of your ECU.
The RP2040 is a powerful, dual-core ARM Cortex-M0+ processor running at 133MHz, offering significantly more processing power than the STM32F103 for roughly the same price.

**Key Advantage for CNC Milling:**
The Pico module has **castellated edges**, meaning you can solder it directly onto a flat PCB surface (SMD style) OR use through-hole headers. This makes it extremely friendly for home CNC PCBs—no fine-pitch LQFP soldering required!

---

## 1. Pin Mapping (Pico to ECU Functions)

The Pico has fewer ADC pins (only 3 user accessible!) compared to STM32, so we must be clever or add an external ADC chip.
*For a basic 4-cylinder EFI, we can just barely fit everything if we use the onboard ADCs carefully.*

### 1.1 Ignition & Injection (Digital Outputs)
*The Pico 3.3V logic is fast and strong.*
- **GP0 (Pin 1):** **INJ1** (Cyl 1&4)
- **GP1 (Pin 2):** **INJ2** (Cyl 2&3)
- **GP2 (Pin 4):** **IGN1** (Cyl 1&4)
- **GP3 (Pin 5):** **IGN2** (Cyl 2&3)
- **GP4 (Pin 6):** **Fuel Pump Relay**
- **GP5 (Pin 7):** **Fan Relay**
- **GP6 (Pin 9):** **Idle Valve PWM** (or Step)
- **GP7 (Pin 10):** **Idle Direction** (if Stepper)

### 1.2 Crank & Cam Inputs
*RP2040 PIO (Programmable I/O) is amazing for high-speed crank decoding!*
- **GP14 (Pin 19):** **Crank Signal** (VR/Hall Input)
- **GP15 (Pin 20):** **Cam Signal** (Optional)

### 1.3 Analog Sensors (The Challenge)
*Pico only has ADC0, ADC1, ADC2 (GP26, GP27, GP28).*
We need: TPS, MAP, CLT, IAT, O2, Batt. (6 inputs).
**Solution:** Use an external **CD4051 SMD (SOIC-16)** Analog Multiplexer connected to one ADC pin.

**Multiplexer Setup (CD4051 SMD):**
- **Select A (Pin 11):** GP18
- **Select B (Pin 10):** GP19
- **Select C (Pin 9):** GP20
- **Common Output Z (Pin 3):** **ADC0 (GP26)**
- **VEE (Pin 7):** Connect to GND (for 0-5V signals)
- **VSS (Pin 8):** Connect to GND
- **VDD (Pin 16):** Connect to 5V (Allows reading 0-5V sensors)
- **Inputs (Y0-Y7):**
  - Y0 (Pin 13): **TPS**
  - Y1 (Pin 14): **MAP**
  - Y2 (Pin 15): **CLT**
  - Y3 (Pin 12): **IAT**
  - Y4 (Pin 1): **O2**
  - Y5 (Pin 5): **Battery Voltage**
  - Y6 (Pin 2): Spare
  - Y7 (Pin 4): Spare

### 1.4 OBD-II & Diagnostic Port
The ECU will include an **OBD-II Female Connector** for under-dash mounting.
- **Pin 4 & 5:** Ground
- **Pin 16:** 12V Battery
- **Pin 6 (CAN High) & Pin 14 (CAN Low):** Connected to a CAN Transceiver (MCP2551/62) - *Optional for later.*
- **Pin 7 (K-Line):** For older diagnostic protocols - *Optional.*
- **Custom Mapping:** We can use the standard OBD-II pins to output UART data to a Bluetooth adapter (HC-05) for mobile apps.

---

## 2. Hardware Design Changes (Vs STM32)

### 2.1 Power Supply (5V -> 3.3V)
The Pico has an onboard buck-boost regulator (RT6150), but for an automotive ECU, we should feed it clean **5V** into the **VSYS** pin.
- **Input:** 12V -> LM7805 (as usual).
- **Output:** 5V -> Pico **VSYS (Pin 39)** and CD4051 **VDD**.
- **GND:** Pico **GND (Pin 38, etc)**.

### 2.2 Level Shifting for Multiplexer
Since the CD4051 is powered by 5V to read 5V sensors, its "Select" pins (A, B, C) might expect 5V logic. However, CD4051 usually accepts 3.3V logic as "High" even when powered at 5V.
- **Verification:** Check the specific brand datasheet. If needed, add a **74HCT125** or simple NPN level shifter.

---

## 3. Parts List (BOM) - Pico ECU Version

| Qty | Component | Value/Part | Package | Description |
| :--- | :--- | :--- | :--- | :--- |
| 1 | **MCU** | **Raspberry Pi Pico** | Module | RP2040 Dual Core. |
| 1 | **Multiplexer** | **CD4051** | **SOIC-16 (SMD)** | 8-Channel Analog Mux. |
| 1 | **Connector** | **OBD-II Female** | Pigtail/Socket | For vehicle diagnostics. |
| 1 | **Regulator** | **LM7805** | TO-220 | 12V to 5V supply. |
| 1 | **Diode** | **1N5408** | Axial | Reverse polarity protection. |
| 1 | **TVS Diode** | **5KP15A** | Axial | Surge protection. |
| 4 | **MOSFET** | **IRLZ44N** | TO-220 | Logic-level Injector Drivers. |
| 2 | **Resistor** | **220Ω** | THT | Ignition protection resistors. |
| 6 | **Resistor** | **1kΩ** | THT | Input protection resistors. |
| 6 | **Capacitor** | **100nF** | Ceramic | Input filters. |
| 6 | **Zener** | **3.3V** | Axial | ADC protection. |
| 2 | **Resistor** | **2.49kΩ** | THT | Temp sensor pull-ups. |
| 1 | **Screw Terminal** | 2-pin | THT | Power input. |
| 1 | **Main Connector** | **Molex/Bosch Style** | Connector | Standard ECU Interface (See Chapter 4). |

---

## 4. Standard ECU Connectors
Is there a "Standard" for ECU connectors? **Yes and No.**

Most DIY and Professional ECUs use one of these three families:

1.  **Bosch 55-Pin (Classic):** Used in millions of BMWs, VWs, and Fiats from the 90s.
    - *Pros:* Extremely robust, waterproof, easy to find in junk yards.
    - *Cons:* Large, many pins you might not need.
2.  **TE Superseal (1.0 or 1.5):** The modern "Standard" for aftermarket ECUs like Haltech or Link.
    - *Pros:* Fully waterproof, very high quality, available in 26, 34, or 60 pins.
    - *Cons:* Expensive, requires special crimping tools.
3.  **Molex CMC / Mini-Fit:** Used in Megasquirt and smaller ECUs.
    - *Pros:* Cheap, compact.
    - *Cons:* Not always waterproof (Mini-Fit), harder to hand-crimp than Bosch.

**Recommendation for your Regata project:**
Use a **DB37 Connector** (like Megasquirt) for the first prototype if it's inside the cabin. If the ECU is in the engine bay, look for a **34-pin TE Superseal** clone or a **used Bosch connector** from a scrap car.

---

## 5. PCB Layout Strategy (CNC 3018)

This is actually the **easiest version to mill**.

1.  **Module Footprint:**
    - You don't need to mill tiny pads.
    - Create a footprint with two rows of 20 holes (2.54mm pitch).
    - Or, mill "long pads" to surface-mount the Pico by its castellated edges.

2.  **Multiplexer (CD4051):**
    - Standard DIP-16 package. Huge pads, easy to drill.

3.  **Routing:**
    - Place Pico in the center.
    - Place CD4051 near the Analog pins (GP26).
    - Route Power and High-Current Drivers on the edges.

---

## 4. Firmware Support (Speeduino for Pico?)
Yes! There is a port of Speeduino for RP2040.
- **Project:** **Speeduino-RP2040** (often called "PicoEFI" or similar forks).
- **Status:** Experimental but running engines.
- **Advantage:** Dual-core allows one core to do nothing but watch the Crank signal (perfect timing) while the other runs the math and USB.

**Verdict:**
If you are comfortable adding one cheap chip (CD4051 Multiplexer), the **Pico version is superior** in processing power and USB connectivity, and easier to mill than the STM32 LQFP version.
