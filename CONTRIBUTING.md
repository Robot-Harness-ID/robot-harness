# Contributing

## Scope

The initial milestone is a reproducible NERO + fixed D435i baseline. New robot abstractions should follow evidence from a working reference implementation rather than anticipated vendor support.

## Evidence

- Label changes as implemented, replay-tested, or hardware-validated.
- Record firmware, driver, dependency, URDF, camera, tool, and calibration revisions with hardware results.
- Split calibration and validation samples before solving parameters.
- Report all trials, including failures, aborts, interventions, and safety stops.
- Do not describe forward-kinematics repeatability or reprojection residuals as independent absolute accuracy.
- Do not commit credentials, API keys, private calibration data, or large raw recordings.

## Changes

Keep changes focused. Specifications and implementations must change together when their behavior changes. Add or update validation for machine-readable artifacts.

## Safety

Pull requests introducing motion must make their preconditions and failure behavior explicit. Agents and calibration routines must not modify independent safety limits or emergency-stop behavior.
