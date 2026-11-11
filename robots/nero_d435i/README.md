# NERO + fixed D435i reference baseline

This directory defines the first reference experiment. It is a protocol, not a validated bring-up package.

## Setup to record

- NERO firmware and mounting orientation
- Exact end effector, tool geometry, payload, and TCP definition
- AgileX ROS driver, SDK, and URDF revisions
- D435i serial number, firmware, RGB/depth modes, and mounting description
- RealSense SDK and ROS wrapper revisions
- Host OS, ROS distribution, network/CAN setup, and time source
- Calibration board geometry, material, target mounting, and measurement method

Do not publish private device or site information in public trial artifacts.

## Execution stages

### B0: read-only health

Do not send motion commands. Verify joint names, order, units, signs, real feedback, URDF agreement, timestamps, stream freshness, camera metadata, coordinate frames, diagnostics, disable/stop behavior, and single-writer control configuration.

A software stop command is not a substitute for an independent physical emergency stop.

### B1: robot without visual targets

Run at low speed in a clear workspace after a site-specific safety review.

- Repeat safe `A -> B -> A` joint motions for 30 cycles.
- Command operator-specified Cartesian targets that avoid limits, singularities, and obstacles.
- Record command and feedback trajectories, settling time, timeouts, and abnormal status.
- Report encoder/FK repeatability only as repeatability, not absolute accuracy.

Do not proceed to hand-eye calibration if these basics are unstable.

### B2: camera and calibration

- Record factory RGB/depth intrinsics and camera-internal extrinsics before changing calibration.
- Validate RGB intrinsics with a rigid matte ChArUco target at the actual operating resolution.
- Keep RGB/depth alignment, optical frames, and `CameraInfo` consistent.
- Capture only after the arm and target are stationary.
- Use approximately 30–40 diverse calibration poses with substantial orientation variation.
- Reserve approximately 15 poses before solving; never use them for parameter selection.
- Record the source and uncertainty of TCP and target-mount transforms.

Factory calibration, camera calibration residuals, robot FK residuals, external metrology, and physical task success are different evidence. Claims must identify which evidence is available.

### B3: perception without an agent

Compare under the same task distribution:

1. Operator-specified base-frame goals.
2. Camera-derived goals with one open-loop execution.
3. Camera-derived goals with bounded reobservation and local correction.

Use an independent physical outcome where possible. Do not use the same camera estimate both to command the robot and to certify its absolute error.

### B4: frozen agent

Only after B0–B3 pass, expose a minimal interface such as `observe`, `move_to_pose`, `approach`, `retreat`, and `stop`. Add `grasp` and `release` only when the end effector is defined and independently tested.

Freeze the model, prompt, tool schema, retry budget, and skill revision for each comparison. Save all tool calls and outcomes.

## Minimum report

- Single-attempt and final success, reported separately
- Wilson 95% confidence intervals for success proportions
- Retry, intervention, timeout, and safety-stop counts
- P50/P95 task duration and settling time
- Held-out calibration metrics with their measurement method
- Calibration time, human operation time, and real interaction count
- Failure counts by control, perception, calibration, execution, planning, and safety category

Thirty trials per condition are exploratory, not proof of high reliability. No numerical performance target is set before the baseline is measured.

## Official references

- [agx_arm_ros](https://github.com/agilexrobotics/agx_arm_ros)
- [pyAgxArm NERO guide](https://github.com/agilexrobotics/pyAgxArm/blob/master/docs/nero/first_time_user_guide_can.md)
- [realsense-ros](https://github.com/realsenseai/realsense-ros)

Use the vendor procedures for firmware selection and CAN setup. Do not copy generic motion examples without checking the actual firmware, tool, workspace, and control mode.
