# Arctic Bloodhound — System Architecture

This document defines the end-to-end system architecture for Arctic Bloodhound, with explicit separation between:
- **Safety-critical flight control** (Pixhawk / PX4)
- **High-level autonomy compute** (Jetson Nano + ROS 2, planned activation)
- **Operator control and ground tooling** (RC + QGroundControl)

The goal is to support reconnaissance autonomy (search, detect, track, follow) without compromising flight safety.

---

## 1. Architectural principles (what we enforce)

### P1 — Flight safety has final authority
The Pixhawk flight controller is the authoritative control system for:
- stabilization
- transitions
- actuator control
- failsafes

High-level autonomy must command the vehicle through approved interfaces rather than bypassing the flight controller.

### P2 — Autonomy is modular and testable
Autonomy functions are decomposed into ROS 2 nodes with clear responsibilities, enabling:
- simulation-first development
- log-driven verification
- incremental capability growth

### P3 — Simulation precedes flight
All autonomy behaviors are validated in SITL with measurable acceptance criteria before hardware activation.

---

## 2. System blocks (what exists and what is planned)

### Air Vehicle (baseline)
- Super Stingray VTOL airframe (Flightory baseline)
- 5-motor propulsion: 4× VTOL + 1× pusher
- 2× control surface servos

### Flight Control (safety-critical)
- Pixhawk 6C Mini (runs PX4)
- PM06 V2 power module (power + current/voltage sensing)
- M10 GPS (position/velocity)

### Operator Control (human authority)
- RadioMaster Pocket transmitter
- BetaFPV ELRS Nano receiver

### Companion Compute (installed, not yet active)
- Jetson Nano (planned autonomy compute)
- Runs ROS 2 autonomy stack (planned)
- Storage: 256 GB microSD + 500 GB SSD (future use)

### Ground Segment
- QGroundControl for mission planning, configuration, and telemetry monitoring
- (Optional) additional GCS/log tooling as the project matures

---

## 3. Interfaces and data flows

This section describes what information moves between subsystems.

### 3.1 RC → Pixhawk (manual control + safety override)
**Purpose:** manual flight, mode switching, emergency abort authority  
**Flow:** RC transmitter → ELRS receiver → Pixhawk RC input

Pixhawk interprets RC commands and can revert to failsafe behavior on RC loss.

### 3.2 Sensors → Pixhawk (state estimation)
**Purpose:** navigation and stabilization  
**Inputs:**
- IMU / barometer (onboard Pixhawk)
- GPS position/velocity (M10 GPS)

**Outputs:**
- estimated attitude, position, velocity
- navigation health for failsafes and mission modes

### 3.3 Pixhawk → Actuators (vehicle motion)
**Purpose:** control authority and stability  
**Outputs:**
- PWM/DSHOT signals to 5× ESCs (motors)
- PWM to 2× servos (control surfaces)

### 3.4 Pixhawk ↔ Ground Control (mission + telemetry)
**Purpose:** mission setup and monitoring  
**Flow:**
- QGroundControl loads missions, configures parameters, monitors status
- Pixhawk publishes telemetry and system health

### 3.5 Jetson (planned) ↔ Pixhawk (autonomy interface)
**Purpose:** high-level autonomy commands while retaining flight safety separation

**Planned autonomy model:**
- Jetson runs ROS 2 autonomy nodes (perception, tracking, mission logic)
- Jetson outputs high-level commands (e.g., velocity or position setpoints)
- Pixhawk executes stabilized control and enforces safety limits

**Important note:**
Jetson is currently installed but not powered/wired, so autonomy compute is not yet active in the flight stack.

---

## 4. Autonomy decomposition (ROS 2 node plan)

This is the planned modular breakdown for perception-driven reconnaissance autonomy.

### 4.1 Mission Manager (ROS 2)
- Executes mission state machine:
  - Search → Detect → Track → Follow → Lost → Reacquire → Abort/RTL
- Decides behavior based on sensor confidence and safety constraints

### 4.2 Perception Node (ROS 2)
- Ingests camera streams (thermal/RGB when integrated)
- Produces detections with confidence scores
- Outputs target position estimates in image frame and (later) world frame

### 4.3 Target Tracker (ROS 2)
- Maintains target track over time
- Smooths detections
- Handles temporary loss and reacquisition logic

### 4.4 Guidance Node (ROS 2)
- Converts mission intent into motion commands:
  - follow at standoff distance
  - orbit/loiter around target
  - approach/inspect behaviors (as defined by rules)

### 4.5 Safety Monitor (ROS 2)
- Monitors constraints and triggers abort behavior:
  - low battery / GPS degradation
  - comms loss thresholds
  - geofence boundaries (if used)
  - maximum bank/tilt/airspeed constraints (as configured)

---

## 5. Operating modes

### Mode A — Manual flight (today)
- RC controls Pixhawk
- Used for bring-up, tuning, and safe testing

### Mode B — Mission flight (PX4 + QGroundControl)
- Pixhawk executes mission/loiter behaviors
- Validates vehicle performance and stability

### Mode C — Autonomy-assisted (planned)
- Jetson autonomy provides guidance commands
- Pixhawk retains stabilization and failsafe authority
- Verified first in SITL, then staged hardware activation

---

## 6. Verification strategy (architecture-driven testing)

Key principle: verify interfaces before features.

**Interface-level tests**
- RC link and failsafe behavior (RC loss)
- GPS availability and navigation health behavior
- Power sensing and low-voltage triggers
- Motor/servo mapping correctness

**Autonomy-level tests (SITL-first)**
- Waypoint + loiter baseline
- Offboard command acceptance and safety limits
- Find-and-follow behavior using simulated targets
- Lost target → reacquire → resume logic

(Full test scenarios and acceptance criteria are defined in `docs/05_test_plan.md`.)

---

## 7. Out-of-scope (explicitly)

To maintain credibility and avoid overclaiming:
- Thermal/RGB payload integration is planned, not yet active
- Jetson autonomy compute is installed but not yet powered/wired
- No claims of fully autonomous target following in flight until verified with metrics

---

## Summary

Arctic Bloodhound is architected as a reconnaissance UAV with:
- **safety-critical flight control on Pixhawk**
- **scalable autonomy on Jetson + ROS 2**
- **simulation-first verification**
- clear authority boundaries and testable interfaces
