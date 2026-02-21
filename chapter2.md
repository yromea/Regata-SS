# Chapter 2 – CKS32F103C8T6 ECU Pinout and Assignments

## 2.1 Overview

This chapter defines a concrete pinout for the main ECU microcontroller:

- MCU: **CKS32F103C8T6** (drop‑in replacement for STM32F103C8T6, ARM Cortex‑M3)
- Package: 48‑pin LQFP style, similar to “Blue Pill” boards

The pinout is designed to support all three modes:

- Pure carb with electronic ignition and safety
- Hybrid / “half injection” (for example, throttle body injection)
- Full EFI (multi‑point injection) for a 4‑cylinder engine such as the **Fiat Regata 85S**

The assignments below can be used both for:

- A custom ECU PCB
- A prototype built around a CKS32F103C8T6/STM32F103C8T6 breakout/Blue Pill style board


## 2.2 Design Principles for Pin Assignment

- Use **timer input capture** pins for crank and optional cam signals
- Use **hardware timer PWM outputs** for injector and ignition control
- Reserve enough **ADC channels** for all key sensors:
  - TPS, MAP, CLT, IAT, O2, battery voltage
- Keep a dedicated **UART** for TunerStudio‑style tuning and logging
- Reserve pins for **SWD debugging** and future **CAN/OBD‑II**
- Keep some spare ADC/PWM/GPIO pins for future features


## 2.3 High-Level Functional Groups

The ECU pinout is organized into these functional groups:

1. Power and reference
2. Clock, reset, and debugging
3. Engine position inputs (crank and cam)
4. Analog sensor inputs (ADC)
5. Injector outputs (4 channels)
6. Ignition outputs (2 channels, wasted spark)
7. Auxiliary outputs (fuel pump, fan, idle valve, spare)
8. Communications (UART for tuning, CAN for future OBD‑II)
9. Status indication (LED) and spare IO


## 2.4 Power, Reference, and Basic Signals

These pins follow standard STM32F103/CKS32F103 design guidelines and are not heavily customized:

- **VDD / VSS**:
  - Multiple VDD (3.3 V) and VSS (GND) pins:
    - Connect to 3.3 V regulator output and ground plane
    - Decouple each VDD with 100 nF (and at least one 4.7–10 µF bulk capacitor)
- **VDDA / VSSA**:
  - Analog supply and ground for the ADC
  - Use LC/RC filtering from 3.3 V to VDDA
  - Connect sensor reference circuits to VDDA/VSSA as recommended
- **NRST**:
  - Connect to reset push button and programming/debug header
  - Ensure a pull‑up and appropriate filtering if needed


## 2.5 Clock, Debug, and Programming

Recommended pins:

- **HSE (external crystal)**
  - OSC_IN / OSC_OUT pins (package‑specific)
  - Use an 8–16 MHz crystal with appropriate load capacitors
  - Used for precise timing of engine events and serial communication

- **SWD (Serial Wire Debug)**
  - **PA13 – SWDIO**
  - **PA14 – SWCLK**
  - Bring these to a 4‑ or 6‑pin header for programming and debugging

- **BOOT0**
  - Used to select boot mode (flash vs bootloader)
  - Connect with resistor network to allow entering the bootloader if needed


## 2.6 Engine Position Inputs (Crank and Cam)

Accurate engine position is critical. Timer input capture pins are used:

- **Crank position input**
  - **PA0 – TIM2_CH1**
  - Function:
    - Receives conditioned signal from crank sensor (VR or Hall via interface circuit)
    - Used to measure tooth timing for RPM and angle
  - Notes:
    - Dedicated to crank; no ADC usage on this pin

- **Cam position input (optional, for future phase)**
  - **PA1 – TIM2_CH2**
  - Function:
    - Receives conditioned signal from cam sensor if sequential injection or more advanced phasing is desired
  - Notes:
    - Can be left unpopulated or reserved on early boards if not used


## 2.7 Analog Sensor Inputs (ADC)

Six primary analog sensors are planned, plus two spare ADC channels for future use.

Assigned ADC pins:

- **TPS (Throttle Position Sensor)**
  - **PA2 – ADC1_IN2**
  - Function:
    - Reads throttle position voltage (typically 0–5 V)

- **MAP (Manifold Absolute Pressure)**
  - **PA3 – ADC1_IN3**
  - Function:
    - Reads pressure sensor output for engine load calculation

- **CLT (Coolant Temperature)**
  - **PA4 – ADC1_IN4**
  - Function:
    - Reads thermistor voltage divider for engine coolant temperature

- **IAT (Intake Air Temperature)**
  - **PA5 – ADC1_IN5**
  - Function:
    - Reads thermistor voltage divider for intake air temperature

- **O2 Sensor (Lambda)**
  - **PB0 – ADC1_IN8**
  - Function:
    - Reads narrowband or wideband controller output voltage

- **Battery Voltage Sense**
  - **PB1 – ADC1_IN9**
  - Function:
    - Reads scaled battery voltage (via resistor divider) for compensation and diagnostics

- **Spare ADC channels**
  - None explicitly assigned beyond the above six; PA6/PA7 remain available as ADC1_IN6/IN7 if needed in future revisions, but in this design they are used for PWM (see below). The hardware can be laid out to allow alternative population if a future firmware rev needs more ADCs.

All sensor inputs must include:

- Appropriate voltage dividers and filters
- Input protection (series resistor, clamping diodes or TVS)
- Proper reference to the ECU’s sensor ground and 5 V reference where needed


## 2.8 Injector Outputs (Full EFI – 4 Cylinders)

The ECU supports up to four injector channels, suitable for:

- Full multi‑point EFI on a 4‑cylinder engine
- Pairing injectors if needed (semi‑sequential or batch)

Hardware PWM outputs are assigned from **TIM4**:

- **INJ1**
  - **PB6 – TIM4_CH1**
  - Function:
    - Injector channel 1 (e.g., cylinder 1)

- **INJ2**
  - **PB7 – TIM4_CH2**
  - Function:
    - Injector channel 2 (e.g., cylinder 2)

- **INJ3**
  - **PB8 – TIM4_CH3**
  - Function:
    - Injector channel 3 (e.g., cylinder 3)

- **INJ4**
  - **PB9 – TIM4_CH4**
  - Function:
    - Injector channel 4 (e.g., cylinder 4)

Notes:

- Each injector output drives a gate driver / MOSFET stage on the ECU PCB.
- High current switching is done off‑chip; MCU pins only drive logic‑level gates.
- Firmware can:
  - Run full sequential (with cam input and correct firmware)
  - Or run semi‑sequential/batch with simpler logic


## 2.9 Ignition Outputs (Wasted Spark – 2 Channels)

For the Fiat Regata 85S 4‑cylinder engine, a **wasted spark** setup with two ignition channels is a good starting point:

- Channel 1: fires cylinders 1 and 4
- Channel 2: fires cylinders 2 and 3

Ignition outputs use **TIM1** PWM channels:

- **IGN1**
  - **PA8 – TIM1_CH1**
  - Function:
    - Wasted spark output for pair 1&4 (via ignition driver/IGBT)

- **IGN2**
  - **PA9 – TIM1_CH2**
  - Function:
    - Wasted spark output for pair 2&3 (via ignition driver/IGBT)

Notes:

- Each ignition output controls an IGBT or dedicated ignition driver IC, which in turn drives the coil.
- TIM1 allows precise dwell control and spark timing.
- Future firmware could add more ignition channels (e.g., coil‑on‑plug) with different pin assignments if needed.


## 2.10 Auxiliary Outputs (Pump, Fan, Idle, Spare)

Several general‑purpose outputs are needed for safety and comfort:

- **Fuel Pump Relay**
  - **PB12 – GPIO**
  - Function:
    - Controls main fuel pump relay
    - ECU logic:
      - Prime on key‑on
      - Run only when engine is rotating/running
      - Cut off on stall or critical fault

- **Cooling Fan Relay**
  - **PB13 – GPIO**
  - Function:
    - Controls radiator fan relay based on coolant temperature and AC request

- **Idle Control Valve (PWM)**
  - **PA6 – TIM3_CH1**
  - Function:
    - PWM control for idle air control valve (stepper or PWM‑type)
  - Notes:
    - The exact driver stage depends on the chosen idle valve (2‑wire PWM, 3‑wire, 4‑wire stepper, etc.)

- **Spare PWM / GPIO Output**
  - **PA7 – TIM3_CH2**
  - Function:
    - Reserved for:
      - Additional relay (e.g., secondary fan, additional pump)
      - Future features (boost control, VVT, etc.)


## 2.11 Communications (Tuning and OBD‑II Future)

### 2.11.1 TunerStudio / PC Tuning UART

Dedicated UART for tuning and logging:

- **USART3 – Main Tuning Interface**
  - **PB10 – USART3_TX**
  - **PB11 – USART3_RX**
  - Function:
    - Connected to a USB‑to‑UART converter (e.g., CH340, CP2102, FTDI)
    - Used by TunerStudio‑style software for:
      - Real‑time tuning
      - Data logging
      - Configuration upload/download

Notes:

- Isolate this UART from critical ignition/injection pins for simpler debugging.
- Keep a clearly labelled connector for easy laptop connection.

### 2.11.2 CAN Bus for OBD‑II (Future)

The CKS32F103C8T6/STM32F103C8T6 includes a bxCAN peripheral. For future OBD‑II over CAN support:

- **CAN Pins (reserved)**
  - **PA11 – CAN_RX (alternate function)**
  - **PA12 – CAN_TX (alternate function)**

Design notes:

- Bring PA11 and PA12 to a transceiver IC (e.g., TJA1050 or equivalent) footprint.
- Connect the transceiver to the OBD‑II connector CAN pins in the cabin.
- If CAN is not used initially:
  - PA11 and PA12 can remain unpopulated / left as test pads or header pins
  - Firmware does not have to enable CAN until later phases


## 2.12 Status LED and Spare IO

- **Status LED**
  - **PC13 – GPIO**
  - Function:
    - Simple onboard LED for:
      - ECU alive / heartbeat
      - Error indication (blink codes)

- **Spare IO Pins**
  - Several pins remain available depending on final PCB:
    - Unused GPIOs on port B and possibly port C
  - Intended uses:
    - Additional switches (e.g., valet mode, map switch)
    - Extra warning lamps or outputs


## 2.13 Summary

This pinout makes the **CKS32F103C8T6** the central brain of the ECU with:

- Hardware timer inputs for crank and cam
- Four dedicated injector outputs
- Two ignition outputs for wasted spark
- Six key analog sensor inputs plus battery voltage
- Dedicated UART for TunerStudio‑style tuning
- Reserved CAN pins for future OBD‑II support
- Auxiliary outputs for fuel pump, fan, idle control, and future features

The next chapter will build on this pinout to define the detailed system architecture and signal conditioning circuits for each of these pins, tailored to the Fiat Regata 85S and to both carb/half‑injection and full EFI configurations.

