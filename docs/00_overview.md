# Arctic Bloodhound — System Overview (CONOPS)

## Purpose

Arctic Bloodhound is a reconnaissance-focused autonomous UAV project designed to explore **endurance-oriented ISR autonomy**, with emphasis on:
- systems integration
- autonomy architecture
- safety-first design
- verification-driven development

The project reflects engineering practices used in defense and autonomy programs, scaled to a non-classified, prototyping environment.

---

## Operational concept

Arctic Bloodhound is intended to operate as a **persistent reconnaissance asset** capable of:
- vertical takeoff and landing from constrained environments
- efficient fixed-wing cruise for extended endurance
- autonomous search, detection, tracking, and follow behaviors
- safe operation under human supervisory control

The system prioritizes **information gathering and persistence**, not payload delivery or high-speed maneuvering.

---

## System boundaries

### In scope
- flight safety and control via PX4
- autonomy and mission logic via ROS 2 (planned activation)
- perception-driven behaviors (find-and-follow)
- SITL-first validation and test documentation

### Out of scope
- weaponization or offensive use
- operational deployment claims
- classified CONOPS or data
- autonomous engagement decisions

---

## Authority and control

- Pixhawk flight controller retains final authority over:
  - stabilization
  - transitions
  - failsafes
- Autonomy provides **high-level intent**, never direct actuator control
- Operator maintains:
  - abort authority
  - mode selection
  - mission oversight

This separation mirrors real aerospace autonomy architectures.

---

## Development approach

1. Define architecture and interfaces
2. Validate baseline flight behavior
3. Implement autonomy in simulation
4. Verify with measurable metrics
5. Stage autonomy onto hardware incrementally

---

## Intended outcomes

This project is designed to demonstrate:
- readiness for junior autonomy / systems engineering roles
- ability to reason about safety, interfaces, and verification
- familiarity with defense-style engineering workflows
