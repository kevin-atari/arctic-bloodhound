# Arctic Bloodhound — Find-and-Follow Autonomy Specification

This document defines the **Find-and-Follow** capability as a formal autonomy behavior with:
- a clear state machine
- inputs/outputs and interfaces
- safety constraints
- testable acceptance criteria

This spec is written to be implementable first in **SITL**, then staged to hardware.

---

## 1. Purpose

**Find-and-Follow** enables Arctic Bloodhound to:
1) search for a target,
2) detect and confirm it,
3) track it over time,
4) follow while maintaining safe standoff,
5) handle loss and reacquisition,
6) abort safely when constraints are violated.

The intent is reconnaissance: maintain awareness and persistence, not intercept or contact.

---

## 2. Definitions

**Target:** an entity of interest detectable by onboard sensing (thermal and/or RGB).  
**Detection:** a single-frame classification output with confidence.  
**Track:** a persistent target estimate over time.  
**Follow:** guidance that maintains standoff distance and keeps target in sensor view.  
**Reacquire:** a behavior to regain track after temporary loss.  
**Abort:** a transition to safe flight behavior under constraints violation.

---

## 3. Assumptions and scope

### In-scope (this spec)
- state machine and autonomy logic
- perception → tracking → guidance interfaces
- safe follow behavior and loss handling
- SITL-first validation

### Out-of-scope (explicit)
- weaponization or offensive behaviors
- autonomous engagement
- human identification or biometric claims
- claiming thermal payload is active before integration/verification

---

## 4. Autonomy state machine

### States
- **IDLE:** autonomy inactive; manual or mission baseline modes
- **SEARCH:** fly search pattern / scan area for detections
- **DETECT:** confirm detection stability above threshold
- **TRACK:** maintain persistent target estimate; compute target motion
- **FOLLOW:** command motion to maintain standoff and keep target in view
- **LOST:** target not visible; freeze guidance / prepare reacquire
- **REACQUIRE:** execute scan/orbit to regain track
- **ABORT:** return to safe behavior (RTL/LOITER/manual handoff)

### State transitions (high level)
- IDLE → SEARCH: operator enables autonomy
- SEARCH → DETECT: detection confidence ≥ threshold for N frames
- DETECT → TRACK: tracker initializes successfully
- TRACK → FOLLOW: operator authorizes follow OR rules allow auto-follow
- FOLLOW → LOST: target confidence drops below threshold for > T seconds
- LOST → REACQUIRE: triggers if search region known
- REACQUIRE → TRACK/FOLLOW: if target reacquired and confirmed
- Any state → ABORT: safety constraint violated or operator abort

---

## 5. Inputs and outputs (interfaces)

### Inputs
**From PX4/Pixhawk (telemetry / state)**
- vehicle pose (position/velocity/attitude)
- flight mode and arming state
- failsafe flags
- battery state

**From perception**
- detections: (timestamp, class, confidence, bounding box / centroid)
- optional: thermal intensity / segmentation mask

**From tracker**
- target state estimate over time
- track confidence and covariance (or equivalent uncertainty)

**From operator / GCS**
- enable autonomy
- authorize follow
- set standoff distance / follow mode
- abort / return-to-home

### Outputs
**To guidance / PX4 interface**
- desired setpoints (position or velocity)
- desired loiter/orbit commands when searching/reacquiring
- abort command request (RTL/LOITER)

---

## 6. Guidance behavior (FOLLOW)

### Follow objective
Maintain a commanded standoff distance while keeping the target within sensor FOV.

**Primary goals**
- keep target centered in camera view (minimize pixel error)
- maintain standoff distance (range control)
- maintain safe altitude and airspeed constraints

**Follow modes (planned)**
- **Follow-behind:** remain behind target heading
- **Orbit-follow:** orbit around target at fixed radius
- **Station-keep:** maintain position while target remains in view (if target static)

---

## 7. Safety constraints (hard limits)

Autonomy must never override safety limits.
If any constraint triggers, transition to ABORT.

**Constraints**
- max speed limit
- max roll/pitch limit
- min altitude / terrain clearance
- geofence boundary (if configured)
- RC loss / telemetry loss behavior
- low battery threshold
- GPS degradation threshold

**Operator authority**
- operator can abort at any time
- operator can disable autonomy at any time
- autonomy must respect required arming/mode conditions

---

## 8. Observability and logging (required)

Every autonomy run must log:
- state transitions with timestamps
- detection confidence timeline
- track confidence timeline
- commanded setpoints
- safety constraint checks
- abort reason (if any)

These logs are required for verification in `docs/06_results.md`.

---

## 9. Acceptance criteria (ties to test plan)

This spec is verified by tests in `docs/05_test_plan.md`:
- **T9 Find target (detection)**
- **T10 Track target (persistence)**
- **T11 Follow target (find-and-follow)**

Minimum pass thresholds (initial)
- detection stability: confidence ≥ threshold for N frames
- tracking persistence: maintain track ≥ 30s with ≤ 3s cumulative loss
- follow performance:
  - standoff distance error ≤ 20%
  - target within FOV ≥ 80% of follow window
  - no safety constraint violations

Thresholds are tuned after initial SITL runs and documented in results.

---

## 10. Implementation plan (incremental)

**Phase 1 (SITL baseline)**
- implement SEARCH + mission manager state machine
- simulate target detections (synthetic or recorded stream)
- log state transitions and validate timing

**Phase 2 (Tracking)**
- add tracker node
- validate persistence and loss handling

**Phase 3 (Guidance + Follow)**
- generate velocity/position setpoints for follow
- enforce safety constraints and abort logic

**Phase 4 (Hardware activation)**
- power/wire Jetson
- validate offboard interface in a controlled environment
- repeat scenarios and compare SITL vs hardware results

