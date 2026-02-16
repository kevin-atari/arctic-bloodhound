# Arctic Bloodhound — Test Plan and Verification

This document defines the verification approach for Arctic Bloodhound.
The goal is to demonstrate autonomy and systems integration with **measurable pass/fail criteria**, validated in **SITL first**, then staged onto hardware.

**Core principle:** verify interfaces before features.

---

## 1. Verification levels

### L1 — Integration & Safety (vehicle interfaces)
Validates the system is wired/configured correctly and fails safely.

### L2 — Mission Baseline (PX4 behaviors)
Validates waypoint, loiter, and mission execution without custom autonomy.

### L3 — Autonomy Behaviors (ROS 2 / Jetson, planned activation)
Validates find/detect/track/follow behavior using repeatable scenarios and metrics.

---

## 2. Test environment

### Primary environment (required)
- PX4 SITL
- QGroundControl
- ROS 2 autonomy stack (as developed)
- Log capture for all runs

### Hardware staging (after SITL acceptance)
- Pixhawk 6C Mini + PM06 V2
- M10 GPS
- 5 motors + 5 ESCs + 2 servos
- RC link (RadioMaster + ELRS Nano)
- Jetson Nano installed (activated later)

---

## 3. Common data to record (every run)

**Required logs**
- PX4/flight log (attitude, position, velocity, mode, failsafe flags)
- Mission timeline (mode changes, waypoint/loiter events)
- Power telemetry (voltage, current)
- Autonomy node logs (ROS 2 topic logs, timestamps, detection confidence if applicable)

**Artifacts**
- A short run summary: scenario, date, configuration, pass/fail
- Plots: position track, speed, altitude, tracking error (as applicable)

---

## 4. Acceptance criteria (project-wide)

A run is considered **valid** only if:
- no uncontrolled instability occurs
- no safety-critical violations occur (unless the test is explicitly for failsafe)
- logs are captured and the run is reproducible

A scenario **passes** only if it meets the scenario-specific thresholds below.

---

## 5. L1 Tests — Integration & Safety

### T1 — RC link + failsafe (RC loss)
**Goal:** verify RC control and safe behavior on RC loss  
**Procedure (SITL or bench):**
1. Arm in a safe mode
2. Confirm manual control response
3. Simulate RC loss / disconnect
**Pass criteria:**
- vehicle enters configured failsafe behavior within expected time
- no uncontrolled actuator output
- failsafe flag is recorded in logs

---

### T2 — Motor mapping + direction sanity
**Goal:** verify correct motor assignments and directions  
**Procedure:**
- command each motor output individually (test mode)
- confirm physical motor location and correct rotation direction
**Pass criteria:**
- each command matches the intended motor position
- no swapped channels
- direction matches configuration

---

### T3 — Control surface sanity (servos)
**Goal:** verify elevon/control surface response  
**Procedure:**
- command pitch/roll inputs and confirm surface deflections
**Pass criteria:**
- correct direction for pitch and roll
- no binding or saturation at neutral trim

---

### T4 — Power telemetry reporting (PM06)
**Goal:** verify voltage/current telemetry integrity  
**Procedure:**
- confirm voltage reading is plausible for 4S LiPo
- apply controlled load (where safe) and observe current response
**Pass criteria:**
- voltage within expected range
- current increases with load
- values are logged

---

## 6. L2 Tests — Mission Baseline (PX4/QGC)

### T5 — Waypoint mission execution (baseline)
**Goal:** prove mission execution without custom autonomy  
**Procedure (SITL):**
1. Load mission in QGroundControl
2. Arm and run AUTO mission
3. Complete mission and RTL
**Pass criteria:**
- completes mission without failsafe
- stays within geofence (if enabled)
- reaches each waypoint within tolerance:
  - horizontal error ≤ 5 m
  - altitude error ≤ 3 m

---

### T6 — Loiter/orbit stability
**Goal:** stable loiter behavior for reconnaissance use  
**Procedure (SITL):**
- command loiter/orbit at fixed altitude and radius
**Pass criteria:**
- loiter radius error ≤ 20% of commanded radius
- altitude error ≤ 3 m
- no oscillatory divergence

---

### T7 — Transition behavior (VTOL ↔ fixed-wing) (when configured)
**Goal:** safe and repeatable transition  
**Procedure:**
- execute transition sequence per configuration
**Pass criteria:**
- transition completes without mode fault
- altitude drop during transition ≤ defined limit (set after first baseline runs)
- no sustained attitude instability

---

## 7. L3 Tests — Autonomy Behaviors (planned)

> These scenarios become active once Jetson Nano + ROS 2 autonomy interface is powered/wired and validated in SITL.

### T8 — Offboard command acceptance + safety limits
**Goal:** verify autonomy can command motion without violating limits  
**Procedure (SITL):**
- autonomy sends velocity/position setpoints for 60 seconds
**Pass criteria:**
- PX4 accepts commands (no rejection)
- speed/tilt remain within configured safety limits
- abort behavior triggers correctly if setpoints stop

---

### T9 — Find target (detection)
**Goal:** detect a target with confidence threshold  
**Procedure (SITL + simulated target OR recorded sensor stream):**
- run perception node and record detections
**Pass criteria:**
- detection confidence ≥ threshold for ≥ N consecutive frames
- false positive rate below threshold in test dataset
(Exact thresholds set after initial data collection.)

---

### T10 — Track target (persistence)
**Goal:** maintain a stable track over time  
**Procedure:**
- target moves across FOV / environment
**Pass criteria:**
- track maintained for ≥ 30 seconds with ≤ 3 seconds cumulative loss
- tracker outputs smooth target state (no extreme jitter)

---

### T11 — Follow target (find-and-follow)
**Goal:** maintain standoff and keep target within sensor view  
**Procedure:**
- mission manager enters FOLLOW state
- guidance commands motion to maintain standoff distance
**Pass criteria:**
- standoff distance error ≤ 20% of command
- target remains within FOV ≥ 80% of the follow window
- vehicle respects speed/tilt limits at all times
- if target is lost:
  - enters REACQUIRE within ≤ 2 seconds
  - returns to SEARCH or resumes FOLLOW when reacquired

---

## 8. Run reporting template (required for every test)

For each run, save a short summary in `data/` or `docs/06_results.md`:

- Test ID:
- Date:
- Environment: SITL / Hardware
- Configuration (PX4 params / autonomy version):
- Result: PASS / FAIL
- Notes:
- Links to logs/plots:

---

## 9. Next actions

1. Implement and pass L1 tests (integration + safety)
2. Implement and pass L2 tests in SITL (mission baseline)
3. Activate L3 tests only after autonomy interface is validated
4. Publish results plots and run summaries in `docs/06_results.md`

