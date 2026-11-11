# Robot Harness

Robot Harness is an open-source identification, execution, and evaluation layer between general-purpose agents and real robots.

The goal is not merely to solve calibration parameters. The goal is to measure how reliably a frozen agent can operate a real robot after a bounded amount of calibration, interaction, and human intervention.

`ID` stands for both **Identification** and **Identity**: every robot should have a versioned description of its geometry, sensing, control semantics, measured capabilities, and calibration health.

> **Status: bootstrap.** This repository currently contains specifications and the first baseline protocol. It does not yet contain a motion runtime. The NERO + D435i reference configuration has not been hardware-validated by this project.

## Principles

- Establish a repeatable baseline without an agent before evaluating an agent.
- Separate robot, perception, calibration, execution, and planning failures.
- Keep calibration and validation poses disjoint.
- Do not infer absolute accuracy from encoder-based forward kinematics or calibration residuals alone.
- Record unsuccessful trials, software versions, calibration versions, and interventions.
- Treat self-improvement as gated updates to external artifacts with validation and rollback, not unrestricted self-modification.

## Architecture

```text
API agent / VLA
      |
      v
Robot Harness
  |- Capability profile and health
  |- Robot adapter and single-writer control semantics
  |- Local execution skills and recovery
  |- Identification and calibration
  |- Execution traces and independent evaluation
      |
      v
Real robot

Independent hardware safety remains outside the agent and learning loop.
```

## First reference configuration

- AgileX NERO 7-DoF arm
- Intel RealSense D435i, fixed in an eye-to-hand setup
- Position control; no MIT control in the initial baseline
- Ubuntu 22.04 and ROS 2 Humble as the proposed reference environment
- Official AgileX and RealSense drivers, with exact versions recorded during bring-up

The baseline is defined in [robots/nero_d435i/baseline.json](robots/nero_d435i/baseline.json) and [robots/nero_d435i/README.md](robots/nero_d435i/README.md).

## Milestones

1. **B0 — Read-only health:** joint and camera observations, coordinate frames, timestamps, diagnostics, and version capture. No motion.
2. **B1 — Robot baseline:** repeated joint motions and oracle Cartesian goals without camera-derived targets.
3. **B2 — Identification:** TCP and hand-eye calibration with held-out validation and versioned artifacts.
4. **B3 — Perception baseline:** compare oracle targets, single-shot visual targets, and bounded visual correction.
5. **B4 — Frozen agent:** expose a small skill interface and evaluate reliability, interventions, retries, and adaptation cost.

A `v0.1.0` release requires B0–B3 to be reproducible from one revision, including all failed trials and an explicit statement of which measurements are independent.

## Repository layout

```text
specs/                 Versioned data contracts
robots/nero_d435i/     First reference setup and baseline protocol
harness_core/          Planned runtime health and execution primitives
harness_id/            Planned calibration and identification tools
harness_eval/          Planned trial execution and reporting tools
```

Directories without contents are planned, not implemented functionality.

## Official hardware resources

- [AgileX ROS 2 driver](https://github.com/agilexrobotics/agx_arm_ros)
- [AgileX Python SDK](https://github.com/agilexrobotics/pyAgxArm)
- [NERO first-time CAN guide](https://github.com/agilexrobotics/pyAgxArm/blob/master/docs/nero/first_time_user_guide_can.md)
- [RealSense ROS wrapper](https://github.com/realsenseai/realsense-ros)

## Validate repository artifacts

The current checks require only Python 3:

```bash
python3 -m json.tool specs/robot-profile.schema.json >/dev/null
python3 -m json.tool specs/trial-result.schema.json >/dev/null
python3 -m json.tool robots/nero_d435i/baseline.json >/dev/null
```

These checks validate JSON syntax, not hardware behavior or JSON Schema conformance.

## Safety

No software in this repository replaces an independent emergency stop, workspace risk assessment, or the robot manufacturer's safety procedures. Initial hardware work must be read-only. Subsequent motion experiments require low speed, a constrained workspace, a single command writer, and a responsible operator outside the robot's path.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Hardware support claims must distinguish implemented, replay-tested, and physically validated functionality.

## License

Apache-2.0. See [LICENSE](LICENSE).
