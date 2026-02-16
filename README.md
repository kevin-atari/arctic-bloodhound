Arctic Bloodhound is my flagship autonomy portfolio project focused on **defense-style UAV autonomy engineering**: integrating **PX4** flight control with **ROS 2** autonomy, validated in **SITL** with a test plan and measurable performance targets.

**Target roles:** Engineer I / Junior — Robotics & Autonomy, GNC / Controls, Navigation & Sensor Fusion, Systems Engineering, Integration & Test

---

## What this project demonstrates
- Systems-level thinking and interface definition
- PX4 ↔ ROS 2 autonomy integration
- MATLAB/Simulink-based control modeling and analysis
- Simulation-in-the-loop (SITL) testing and verification
- Engineering documentation, test plans, and results

---

## Current status
- PX4 SITL: Environment operational, mission testing in progress
- ROS 2 autonomy stack: Architecture defined, initial nodes planned
- MATLAB/Simulink models: Early control modeling and analysis in progress
- Hardware integration: Airframe assembled, avionics integrated
- Companion compute: Jetson Nano installed, not yet powered or wired
- Test plan and metrics: Initial scenarios and acceptance criteria defined

---

## System overview
**High-level architecture**
- PX4 handles low-level flight control and stabilization
- ROS 2 provides autonomy, mission logic, and offboard control
- QGroundControl used for mission planning and configuration
- MATLAB/Simulink used for modeling, tuning, and performance analysis

---

## Tech stack
**Autonomy & Robotics**
- PX4 (Pixhawk 6C Mini)
- ROS 2
- QGroundControl

**Controls & Modeling**
- MATLAB
- Simulink

**Companion Compute (installed, not yet active)**
- NVIDIA Jetson Nano (vision and autonomy expansion path)

**Languages & Tools**
- C++
- Python
- Linux
- Git / GitHub

---

## Repository structure

docs/        – system documentation, architecture, and test plans  
sim/         – PX4 SITL configurations and scenarios  
software/    – ROS 2 workspace and PX4 configuration  
hardware/    – CAD, wiring, and integration notes  
data/        – logs, plots, and analysis artifacts  

---

## Baseline airframe and attribution

The physical airframe used in Arctic Bloodhound is based on the **Flightory Super Stingray** platform.  
Flightory provides the original airframe geometry, structural design, and manufacturing files.

This project does **not** claim authorship of the original airframe design.

Autonomy, controls, and mission behavior are developed independently of the airframe design.

**My work focuses on:**
- Autonomy and flight control integration (PX4 + ROS 2)
- Systems architecture and interface definition
- Controls modeling and validation (MATLAB/Simulink)
- Simulation (SITL) and test scenario development
- Hardware integration, configuration, and mission-level behavior
- Verification through logs, metrics, and documented test cases

The Flightory platform is used as a **baseline air vehicle** to enable realistic autonomy and systems engineering work.

---
## Air Force Innovation Context

Arctic Bloodhound originated as a **non-classified exploratory project** developed in partnership with an **Air Force innovation organization**, with the goal of advancing hands-on engineering experience in autonomy, systems integration, and UAV mission concepts.

The work focused on:
- rapid prototyping
- autonomy experimentation
- systems architecture and verification practices

This repository represents **independent engineering development and documentation** based on that experience.
It does not represent an operational system, program of record, or official Air Force capability.

All content in this repository is unclassified and suitable for public release.

## Roadmap
**Near-term**
- End-to-end PX4 SITL mission with ROS 2 offboard control
- Logged runs with plots and metrics
- Initial MATLAB/Simulink control model validation

**Mid-term**
- Navigation / estimation experiments
- Expanded test scenarios and verification coverage
- Hardware-in-the-loop preparation

---

## Author
**Kevin Atari**  
USAF Veteran — Robotics Engineering (M.S., in progress)  
Active Secret clearance (TS/SCI eligible)  
Separation: May 18, 2026
