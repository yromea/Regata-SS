# Standalone Custom PCB Design (Bare MCU)

This document describes the netlist and layout guidelines for soldering a **bare CKS32F103C8T6 (or STM32F103RCT6)** chip onto a custom CNC-milled PCB.

**Difficulty:** High (Fine Pitch Soldering & Milling).
**Goal:** Create a professional-looking ECU without the "Arduino/BluePill" module look.

---

## 1. Minimal System Circuit (The "Brain")
*This section replaces the Blue Pill module.*

### 1.1 Power Pins (MCU)
- **VDD_1, VDD_2, VDD_3, VBAT** -> Connect to **+3.3V**.
- **VSS_1, VSS_2, VSS_3** -> Connect to **GND**.
- **VDDA** -> Connect to **+3.3V** (via Ferrite Bead or 10Ω resistor for noise filtering).
- **VSSA** -> Connect to **GND**.
- **Decoupling:** Place a **100nF (0.1µF)** capacitor close to EACH VDD/VDDA pin pair.

### 1.2 Reset Circuit (NRST)
- **NRST (Pin 7)** -> **10kΩ Pull-up** -> **+3.3V**.
- **NRST** -> **100nF Capacitor** -> **GND**.
- **NRST** -> **Tactile Switch** -> **GND** (Manual Reset).

### 1.3 Boot Selection (BOOT0, BOOT1)
*Critical for programming via UART if SWD fails.*
- **BOOT0 (Pin 44)** -> **10kΩ Pull-down** -> **GND**. (Normal Boot).
  - *Add a jumper to pull it to 3.3V for UART Flashing Mode.*
- **BOOT1 (Pin 20)** -> **10kΩ Pull-down** -> **GND**.

### 1.4 Clock (HSE Oscillator)
- **OSC_IN (Pin 5)** -> **8MHz Crystal** Pin 1.
- **OSC_OUT (Pin 6)** -> **8MHz Crystal** Pin 2.
- **Crystal Pin 1** -> **22pF Capacitor** -> **GND**.
- **Crystal Pin 2** -> **22pF Capacitor** -> **GND**.
- *Layout Note: Keep crystal traces extremely short and symmetric!*

### 1.5 Debug Interface (SWD)
- **SWDIO (Pin 34)** -> Header Pin.
- **SWCLK (Pin 37)** -> Header Pin.
- **GND** -> Header Pin.
- **3.3V** -> Header Pin.

---

## 2. Power Supply Section (12V -> 3.3V)
*Since we don't have the Blue Pill's regulator, we must build it.*

1.  **12V Input** -> **Diode (1N5408)** -> **TVS Diode (5KP15A)** to GND.
2.  **12V** -> **LM7805 Vin**.
3.  **LM7805 Vout (5V)** -> **AMS1117-3.3 Vin**.
4.  **AMS1117 Vout (3.3V)** -> **MCU VDD Pins**.
5.  **Filtering:** 470µF on 12V, 100µF on 5V, 10µF Tantalum on 3.3V.

---

## 3. Peripheral Sections (Same as Trial 1)
*Refer to `trial1.md` for the MOSFET and Sensor Input circuits. They are identical.*

- **Injectors:** PB6, PB7 -> MOSFET Gate Circuit.
- **Ignition:** PA8, PA9 -> Logic Level Out / IGBT Gate.
- **Sensors:** PA0-PA5 -> RC Filter + 3.3V Zener Protection.
- **UART:** PB10 (TX), PB11 (RX) -> 4-pin Header.

---

## 4. PCB Layout Strategy (CNC Special)

### 4.1 The "Manhattan" or "Island" Style (Optional)
If milling 0.5mm pitch pads is too hard for your V-bit:
1.  Mill the larger tracks (Power, MOSFETs, Connectors).
2.  Use a **Breakout Board (Adapter)** for the LQFP-48 chip that converts it to DIP pins.
3.  Solder the Adapter onto your CNC board using 2.54mm headers.
   *This is a middle-ground between "Blue Pill Shield" and "Full Custom".*

### 4.2 Full Custom Layout Tips
1.  **Bit Selection:** You need a **0.1mm 10-degree V-Bit** for the MCU pads.
2.  **Leveling:** Use **Auto-Leveling (Height Map)** in your CNC software (Candle/bCNC). If the board is warped by even 0.05mm, the MCU pads will be cut too deep or not at all.
3.  **Clearance:** Set clearance in KiCad/EasyEDA to **0.25mm** for the MCU area, and **0.4mm** elsewhere.
4.  **Routing:**
    - Route the Crystal FIRST.
    - Route the Decoupling Caps SECOND.
    - Route Power Lines THIRD.
    - Route IO signals LAST.

---

**Next Step:** Choose your path.
- **Path A (Trial 1):** Shield for Blue Pill (Easier, faster success).
- **Path B (Standalone):** Full custom board (Professional, but requires precise CNC calibration).
