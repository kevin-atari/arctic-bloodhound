# Arctic Bloodhound — Software Overview

This directory contains the software components for Arctic Bloodhound, organized to support **simulation-first autonomy development**.

---

## Structure (planned)

- `px4/` — PX4 configuration and SITL launch files
- `ros2_ws/` — ROS 2 workspace for autonomy nodes
- `scripts/` — helper scripts for simulation and logging
- `configs/` — parameter and mission configurations

---

## Simulation-first workflow

All autonomy development follows this order:

1. PX4 SITL validation
2. ROS 2 autonomy development against SITL
3. Log-based verification
4. Hardware staging after acceptance

---

## Example SITL workflow (placeholder)

> These commands will be finalized as the autonomy stack is activated.

```bash
# Launch PX4 SITL
make px4_sitl gazebo

# Launch ROS 2 autonomy stack
source ros2_ws/install/setup.bash
ros2 launch autonomy bringup.launch.py

# Monitor vehicle
QGroundControl.AppImage
