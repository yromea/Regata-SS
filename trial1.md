# Trial 1 PCB Design - "The Shield"

This document describes the layout and connections for the first prototype PCB, designed to be milled on a **CNC 3018 Pro**.

## 1. Design Concept
- **Type:** Single-Sided PCB (Bottom Copper Layer only).
- **MCU Mounting:** Socketed "Blue Pill" module (using 2x20 female headers).
- **Trace Width:** 0.6mm (signal), 1.0mm (power/GND) - easy to mill.
- **Clearance:** 0.3mm - 0.4mm (safe for V-bits).
- **Jumpers:** Top-side wire jumpers used where traces would cross (simpler than double-sided milling).

## 2. Pin Connections (Netlist)

### 2.1 Power Section
- **12V Input (Terminal Block)** -> Diode Anode -> Diode Cathode -> **LM7805 Vin**.
- **GND Input** -> Common Ground Plane -> **LM7805 GND**.
- **LM7805 Vout (5V)** -> **Blue Pill 5V Pin**.
- **Capacitors:** 470µF across 12V/GND, 100µF across 5V/GND.

### 2.2 Injector Drivers (Low Side Switch)
*Connector: 4-pin Screw Terminal (12V, INJ1, INJ2, GND)*
- **INJ1 Signal:**
  - Blue Pill **PB6** -> 100Ω Resistor -> **Mosfet Gate 1**.
  - Mosfet Source 1 -> **GND**.
  - Mosfet Drain 1 -> **Injector Connector Pin 2**.
  - 10kΩ Pull-down from Gate to GND.
  - Flyback Diode: Cathode to 12V, Anode to Drain.
- **INJ2 Signal:**
  - Blue Pill **PB7** -> 100Ω Resistor -> **Mosfet Gate 2**.
  - (Repeat circuit for Ch2).

### 2.3 Ignition Outputs (Logic Level)
*Connector: 3-pin Header (GND, IGN1, IGN2)*
- **IGN1 Signal:**
  - Blue Pill **PA8** -> 220Ω Resistor -> **Header Pin 2**.
- **IGN2 Signal:**
  - Blue Pill **PA9** -> 220Ω Resistor -> **Header Pin 3**.

### 2.4 Sensor Inputs
*Connector: 1x8 Pin Header (GND, 5V, TPS, MAP, CLT, IAT)*
- **TPS (PA2):** Pin -> 1kΩ Resistor -> PA2. (Add 3.3V Zener to GND).
- **MAP (PA3):** Pin -> 1kΩ Resistor -> PA3. (Add 3.3V Zener to GND).
- **CLT (PA4):**
  - Pin -> 1kΩ Resistor -> PA4.
  - **Bias:** 2.49kΩ Resistor from **5V** to PA4.
- **IAT (PA5):**
  - Pin -> 1kΩ Resistor -> PA5.
  - **Bias:** 2.49kΩ Resistor from **5V** to PA5.

### 2.5 Crank Input (Simple Opto)
- **Input Terminal:** 2-pin (Signal, GND).
- **Circuit:**
  - Signal -> 1kΩ Resistor -> Opto Anode.
  - Opto Cathode -> GND.
  - Opto Collector -> 3.3V (Blue Pill 3.3V pin).
  - Opto Emitter -> **PA0**.
  - *Note: Check polarity of your specific VR/Hall sensor. Hall sensors need a pull-up on the input side.*

## 3. PCB Layout Guidelines (for CNC)

1.  **Placement:**
    - Place the **Blue Pill headers** in the center.
    - Place **Power Supply** (7805, caps, terminals) on the LEFT edge.
    - Place **High Current Outputs** (Mosfets, terminals) on the RIGHT edge.
    - Place **Sensor Inputs** on the TOP edge.

2.  **Routing Strategy:**
    - Route **GND** first. Try to keep a solid ground path or pour.
    - Route **12V** and **5V** power lines next (thick tracks).
    - Route **Injector Signals** (short and thick).
    - Route **Sensor Signals** (keep away from injector tracks).

3.  **Isolation Milling:**
    - Use a **FlatCAM** or similar software to generate G-code from Gerber.
    - Set tool diameter to your V-bit tip (e.g., 0.1mm or 0.2mm).
    - **Multipass:** Use 2-3 passes for isolation to ensure easy soldering (wider gaps).

4.  **Drilling:**
    - Generate a separate Drill G-code file.
    - Recommended: Run a "spot drill" pass with the V-bit first to center the holes, then swap to drill bits.

## 4. Testing "Trial 1"
1.  **Visual Inspection:** Check for un-milled copper bridges (shorts) between traces using a multimeter / continuity mode. **Critical!**
2.  **Power Test:** Connect 12V *without* the Blue Pill inserted. Measure the 5V pin on the socket. It must be 4.9V - 5.1V.
3.  **Insert MCU:** Plug in the Blue Pill. Check if the Power LED on the module lights up.
4.  **Load Firmware:** Flash a test "Blinky" or Speeduino firmware to toggle PB6/PB7 and verify MOSFET switching with a small bulb or LED load.

---

**Next Step:** Input this netlist into your PCB software (KiCad/EasyEDA) to generate the Gerber files for your CNC.
