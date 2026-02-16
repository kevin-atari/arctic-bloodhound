# Arctic Bloodhound — Hardware Overview

This document defines the **actual hardware baseline** used for the Arctic Bloodhound UAV.
It distinguishes between currently active systems and planned autonomy expansion components.

The design prioritizes **flight safety, endurance, and future autonomy growth**, consistent with reconnaissance-focused UAV architectures.

---

## 1. Airframe & Structure

**Baseline airframe**
- Flightory Super Stingray VTOL airframe
- 3D-printed / composite flying wing structure

**Structural features**
- 4× VTOL motor mounts (vertical lift)
- Rear pusher motor mount
- Internal electronics bay
- Dedicated battery bay
- Wing-mounted control surfaces (elevons or equivalent)

**Purpose**
The airframe enables vertical takeoff and landing combined with efficient fixed-wing cruise, allowing operation from austere environments while maintaining long endurance for reconnaissance missions.

---

## 2. Power System

**Components**
- Gens Ace 6200 mAh 4S LiPo battery
- XT60 main power connector
- PM06 V2 power module (14S capable)

**Functions**
- Supplies power to all onboard systems
- Regulates and delivers safe voltage to the flight controller
- Measures battery voltage and current
- Distributes power to ESCs

**Purpose**
The power system supports long-duration missions, enables accurate energy monitoring, and provides safe electrical isolation between high-power propulsion and sensitive avionics.

---

## 3. Flight Control System

**Component**
- Pixhawk 6C Mini flight controller

**Functions**
- Reads onboard sensors (IMU, barometer)
- Executes real-time control loops
- Commands motors and servos
- Manages VTOL ↔ fixed-wing transitions
- Enforces failsafes (RC loss, low battery, RTL)

**Purpose**
Pixhawk serves as the authoritative flight and safety controller. All autonomy and higher-level decision-making is architected to operate through this controller rather than bypass it.

---

## 4. Navigation System

**Component**
- M10 GPS module

**Functions**
- Provides global position (latitude/longitude)
- Supplies ground speed and heading
- Supports waypoint navigation and loiter behavior

**Purpose**
Navigation data underpins all mission execution, safety behaviors, and future target-relative autonomy functions.

---

## 5. Radio Control System

**Components**
- RadioMaster Pocket transmitter
- BetaFPV ELRS Nano receiver
  - Receiver board
  - 2.4 GHz IPEX antenna
  - Optional pin headers
  - 4-wire solder cable

**Functions**
- Manual flight control
- Mode switching
- Emergency override and abort authority

**Purpose**
The radio control system provides human-in-the-loop supervision, which is essential for testing, safety, and regulatory compliance even in autonomous missions.

---

## 6. Propulsion System

**Components**
- 5 × brushless motors
  - 4 × VTOL lift motors
  - 1 × rear pusher motor
- 5 × Predator 40A ESCs (individual)

**Functions**
- Vertical lift during takeoff and landing
- Efficient forward propulsion during cruise
- Independent motor control for stability and transitions

**Purpose**
This propulsion configuration enables VTOL operation while preserving the endurance advantages of fixed-wing flight.

---

## 7. Control Surfaces

**Components**
- 2 × Spektrum A6380 digital servos

**Functions**
- Actuate wing control surfaces
- Control pitch and roll during forward flight

**Purpose**
Control surfaces reduce reliance on thrust-based control during cruise, improving energy efficiency and flight stability during reconnaissance loitering.

---

## 8. Companion Computing (Installed, Not Active)

**Components**
- NVIDIA Jetson Nano
- Samsung 256 GB microSD card (operating system)
- SK Hynix Gold P31 500 GB SSD (future use)

**Current status**
- Physically installed
- Not powered or wired
- No control authority

**Planned functions**
- Computer vision processing
- AI inference
- Target detection and tracking
- Find-and-follow autonomy behaviors

**Purpose**
The companion computer provides a scalable path toward perception-driven autonomy while maintaining strict separation from flight safety systems.

---

## 9. Cabling, Connectors, and Integration Hardware

**Components**
- Pixhawk power cable (PM06 → Pixhawk)
- ESC signal cables (3-wire)
- Servo cables
- Receiver solder wires
- XT60 battery connector
- Receiver antenna cable
- Mounting hardware (screws, spacers)
- Foam padding and vibration isolation

**Purpose**
These components ensure reliable electrical connections, minimize vibration-induced sensor noise, and support maintainable system integration.

---

## Summary

The Arctic Bloodhound hardware architecture emphasizes:
- Separation of safety-critical flight control from autonomy compute
- Endurance-focused propulsion and power design
- Modular expansion toward perception-driven autonomy

This hardware baseline supports reconnaissance mission profiles while allowing incremental autonomy development and verification.
