# Classic Carb and EFI ECU Project (Egypt-Sourced Components)

## Project Vision

This project aims to design and build an open, affordable ECU platform that can:

- Control both classic carbureted and modern EFI engines
- Allow gradual conversion:
  - From full carburetor to “half injection” (for example, throttle body injection)
  - From full carburetor to full multi-point injection
- Improve safety, reliability, and drivability for daily use and long-distance trips
- Use components realistically available in the Egyptian electronics market

The ECU will support:

- Pure carb engines with electronic ignition and safety functions
- Hybrid setups (carb plus some electronic injection or electronic fuel control)
- Full EFI conversions with fuel and ignition under ECU control

The goal is that any owner of a classic carb engine in Egypt can:

- Build or buy this ECU
- Wire it safely using documented harnesses
- Tune it using TunerStudio-like tools
- Diagnose issues using an OBD-II style connector or serial tuning connection

## Personal Motivation and Story

The project is driven by a real safety incident. While driving on the desert highway between Cairo and Alexandria, in a classic carbureted car, the engine bay caught fire without immediate notice. The road was nearly empty, and the destination was far away. Only by the goodness and mercy of God did this not end in tragedy.

This experience showed how dangerous unmanaged fuel systems and old carburetor setups can be, especially when combined with old wiring and fuel hoses. A more intelligent, monitored, and controllable fuel and ignition system can drastically reduce such risks.

This project is meant to turn that near-disaster into something positive and useful for others who drive similar cars.

## Author and Contact

- Name: Eng. Ebram Dawood
- Phone: +2 01558395837

## Initial Reference Vehicle

The first target vehicle for development, testing, and full conversion is:

- Fiat Regata 85S
- With air conditioning
- Originally full carburetor

The plan is to:

- Convert this Fiat Regata 85S from full carb to full injection
- Validate ECU safety functions on this real car
- Produce wiring diagrams, base maps, and installation notes that other Fiat Regata owners and similar classic car owners can follow

## High-Level Objectives

- Safety:
  - Fuel pump and injector control with automatic cut-off on stall or fault
  - Proper fusing and electrical protection
  - Engine bay monitoring with optional additional sensors
- Control:
  - Support for ignition and fuel on carb, hybrid, and full EFI setups
  - Stable idle, better cold starts, and smoother driving
- Diagnostics and Tuning:
  - TunerStudio-compatible communication
  - Real-time gauges and logging
  - OBD-II style connector in the cabin

## Project Structure

The project is organized into chapters, from research to manufacturing:

1. Egypt Market Research for Components
2. System Architecture for Carb, Hybrid, and EFI Setups
3. Electronics and PCB Design
4. Firmware and Software Architecture
5. Tuning, Diagnostics, and OBD-II Interface
6. Prototype Manufacturing and Assembly
7. Vehicle Integration, Testing, and Safety Validation
8. Documentation and Community Adoption

The following document, chapter1.md, begins with detailed research into the Egyptian electronics market and available components for building such an ECU.

