# Chapter 2R – STM32F103RCT6 ECU Pinout and Assignments

## 2R.1 Overview

This chapter describes a dedicated ECU pinout for:

- MCU: **STM32F103RCT6** (ARM Cortex‑M3, 64‑pin LQFP, larger flash and RAM)

The functional goals are the same as the CKS32F103C8T6 version:

- Pure carb with electronic ignition and safety
- Hybrid / “half injection” (for example, throttle body injection)
- Full EFI (multi‑point injection) for a 4‑cylinder engine such as the **Fiat Regata 85S**

Compared to the 48‑pin CKS32F103C8T6, the STM32F103RCT6 version:

- Exposes more GPIO and ADC pins for expansion
- Provides more flash and RAM for advanced firmware features


## 2R.2 Design Principles for Pin Assignment

- Keep the **same core functional pins** as the CKS32F103C8T6 version where possible:
  - Crank and cam
  - 4 injector outputs
  - 2 ignition outputs (wasted spark)
  - Key sensors (TPS, MAP, CLT, IAT, O2, battery sense)
  - Pump, fan, idle control, tuning UART, CAN, debug, status LED
- Use **additional pins** only for expansion:
  - Extra sensors (oil temp/pressure, fuel pressure, boost)
  - Extra relays and actuators (secondary fan, auxiliary pumps, VVT, boost control)


## 2R.3 High-Level Functional Groups

1. Power and reference
2. Clock, reset, and debugging
3. Engine position inputs (crank and cam)
4. Analog sensor inputs (ADC)
5. Injector outputs (4 channels, EFI)
6. Ignition outputs (2 channels, wasted spark)
7. Auxiliary outputs (fuel pump, fan, idle valve, extra relays)
8. Communications (UART for tuning, CAN for future OBD‑II)
9. Status indication (LED) and spare IO
10. Expansion pins (sensors and actuators)


## 2R.4 Power, Reference, and Basic Signals

- **VDD / VSS**:
  - Multiple 3.3 V and GND pins
  - Decouple each VDD with 100 nF and add bulk capacitance

- **VDDA / VSSA**:
  - Analog supply and ground for the ADC
  - Filtered from 3.3 V and used as reference for sensor circuits

- **NRST**:
  - Connected to reset button and programming/debug header


## 2R.5 Clock, Debug, and Programming

- **HSE Crystal**:
  - External crystal on OSC_IN/OSC_OUT pins (8–16 MHz)

- **SWD Debug**:
  - **PA13 – SWDIO**
  - **PA14 – SWCLK**

- **BOOT0**:
  - Wired with resistor network to allow entering bootloader mode if needed


## 2R.6 Engine Position Inputs (Crank and Cam)

- **Crank position input**
  - **PA0 – TIM2_CH1**
  - Receives conditioned signal from crank sensor (VR or Hall)

- **Cam position input (optional)**
  - **PA1 – TIM2_CH2**
  - Receives conditioned signal from cam sensor for sequential or more advanced modes


## 2R.7 Analog Sensor Inputs (ADC)

Base sensor set (same mapping as the CKS version):

- **TPS (Throttle Position Sensor)**
  - **PA2 – ADC1_IN2**

- **MAP (Manifold Absolute Pressure)**
  - **PA3 – ADC1_IN3**

- **CLT (Coolant Temperature)**
  - **PA4 – ADC1_IN4**

- **IAT (Intake Air Temperature)**
  - **PA5 – ADC1_IN5**

- **O2 Sensor (Lambda)**
  - **PB0 – ADC1_IN8**

- **Battery Voltage Sense**
  - **PB1 – ADC1_IN9**

All sensor inputs use:

- Voltage dividers and filters
- Input protection and proper sensor ground/reference

Expansion sensor inputs using extra ADC pins on the 64‑pin package:

- **Oil Temperature**
  - Example: **PC0 – ADC1_IN10**

- **Oil Pressure**
  - Example: **PC1 – ADC1_IN11**

- **Fuel Pressure or Boost Pressure**
  - Example: **PC2 – ADC1_IN12**

These additional channels are optional and can be populated as needed.


## 2R.8 Injector Outputs (Full EFI – 4 Cylinders)

Use TIM4 outputs, same as in the 48‑pin design:

- **INJ1**
  - **PB6 – TIM4_CH1**

- **INJ2**
  - **PB7 – TIM4_CH2**

- **INJ3**
  - **PB8 – TIM4_CH3**

- **INJ4**
  - **PB9 – TIM4_CH4**

Each output drives an external MOSFET/driver stage for the injector.


## 2R.9 Ignition Outputs (Wasted Spark – 2 Channels)

Use TIM1 outputs:

- **IGN1 (cyl 1&4)**
  - **PA8 – TIM1_CH1**

- **IGN2 (cyl 2&3)**
  - **PA9 – TIM1_CH2**

Each output drives an ignition driver/IGBT that controls an ignition coil.


## 2R.10 Auxiliary Outputs (Pump, Fan, Idle, Extra)

Base outputs:

- **Fuel Pump Relay**
  - **PB12 – GPIO**

- **Cooling Fan Relay**
  - **PB13 – GPIO**

- **Idle Control Valve (PWM)**
  - **PA6 – TIM3_CH1**

- **Spare PWM / Output**
  - **PA7 – TIM3_CH2**

Additional outputs using extra pins (examples):

- **Aux Relay 1 (e.g., secondary fan or pump)**
  - Example: **PC4 – GPIO**

- **Aux Relay 2**
  - Example: **PC5 – GPIO**

- **Boost Control / VVT / Other PWM Actuator**
  - Example: **PB5 – TIM3_CH2 (alternate mapping)** or another available timer channel

Exact use of these extra outputs can be defined per hardware revision.


## 2R.11 Communications (Tuning and OBD‑II Future)

- **TunerStudio / PC Tuning UART**
  - **USART3**
    - **PB10 – USART3_TX**
    - **PB11 – USART3_RX**
  - Connected to USB‑to‑UART module for tuning and logging

- **CAN Bus for OBD‑II**
  - **PA11 – CAN_RX**
  - **PA12 – CAN_TX**
  - Connected to CAN transceiver and OBD‑II connector CAN pins

Additional interfaces (using extra pins on the 64‑pin package) can include:

- Extra UARTs (e.g., for GPS or external logger)
- SPI/I²C for displays, external ADCs, IO expanders


## 2R.12 Status LED and Spare IO

- **Status LED**
  - **PC13 – GPIO**
  - Heartbeat and error indication

- **Spare IO Pins**
  - Remaining unused pins on ports A, B, and C
  - Reserved for:
    - Switch inputs (map switch, valet mode, debug buttons)
    - Extra lamps and indicators


## 2R.13 Summary

The STM32F103RCT6 ECU version:

- Keeps the core functional mapping aligned with the CKS32F103C8T6 design
- Adds extra ADC channels and GPIO for more sensors and actuators
- Provides more flash and RAM for richer firmware, diagnostics, and logging

This chapter can be used in parallel with the CKS32F103C8T6 pinout to design:

- A compact ECU using the 48‑pin MCU
- An expanded ECU using the 64‑pin MCU for more advanced configurations

