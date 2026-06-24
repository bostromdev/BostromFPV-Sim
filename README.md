# Bostrom FPV Sim v0.1.2-beta

**Bostrom FPV Sim** is a Godot 4.7 freestyle FPV simulator built around a custom quadcopter flight model—not a stock `RigidBody3D` with arcade controls.

v0.1.0 was the proof of concept: a simple drone that could arm, take input, and fly.

v0.1.1 established the simulator foundation with custom fixed-step flight physics, per-motor response, FPV-style rate control, telemetry, collision physics, controller calibration, and a purpose-built freestyle warehouse.

**v0.1.2-beta focuses on flight feel, collision consistency, camera behavior, and map cleanup.**

> This is still early-access software. The goal is a believable freestyle FPV simulator that can be validated against real flight behavior and telemetry—not artificial stability or arcade movement.

---

## What’s New in v0.1.2-beta

### Flight Feel and Momentum

* Improved tilt-to-thrust behavior so hard roll and pitch moves carry more believable momentum, carving, and altitude loss.
* Reduced the overly automatic “catch” feeling during fast stick changes.
* Improved I-term relaxation so aggressive reversals feel less locked-in while steady corrections remain usable.
* Continued reducing hidden-feeling stabilization so the quad reacts more honestly to angle, throttle, inertia, drag, and motor response.

### Collision and Surface Contact

* Improved friction consistency across collision objects for more natural scrapes, skids, wall contact, and ground interaction.
* Cleaned up collision alignment around tower trim and surrounding map geometry.
* Continued refining collision behavior so contact redirects the quad through physical impulse and rotation instead of fake stopping or auto-settling behavior.

### Map and Rendering Cleanup

* Repaired dive-tower roof seams, tower shells, and surrounding geometry.
* Fixed several see-through surfaces, reversed faces, hollow-looking structures, and close-range rendering issues.
* Improved camera near-clip behavior when flying close to walls, towers, and obstacles.
* Cleaned up tower trim placement and collision alignment.
* Continued improving visual consistency between visible objects and their collision geometry.

---

## Core Simulator Features

### Flight Model

* Custom deterministic flight simulation at **240 Hz**, with a **120 Hz low-spec mode**.
* Individual motor and ESC response including spool-up, braking, RPM, thrust, current draw, motor/prop behavior, and battery-voltage sag.
* Quad-X motor mixing, body inertia, gravity, directional thrust, drag, dynamic idle, throttle response, and motor desaturation.
* Betaflight-inspired Actual Rates, PID, feedforward, I-term relax, anti-gravity, RC smoothing, filtering, deadband, and thrust-response controls.
* Default rate baseline: **210 center sensitivity / 700 max rate / 0.54 expo** on roll, pitch, and yaw.

### Air and Momentum

* Ground effect, aerodynamic drag, dirty-air/propwash approximation, wake re-entry, turbulence, authority loss, and recovery behavior.
* Reduced artificial passive damping so throttle cuts, dives, split-S moves, powerloops, and catch-outs can remain more ballistic.
* Thrust-vector and vertical-response checks to prevent camera tilt from incorrectly changing physical thrust direction.

### Collision

* Component-aware collision checks for the frame, arms, motors, prop discs, battery, and camera.
* Contact impulse uses collision point, surface normal, contact velocity, mass, inertia, friction, and restitution.
* Normal impacts do not automatically zero momentum, auto-level the quad, force a fake settle, or disarm it.
* Overlap recovery and collision-event filtering reduce repeated contact spam from resting or minor scrape contact.

### Controls and Tuning

* Guided generic HID controller calibration for roll, pitch, throttle, yaw, endpoints, direction, and arming.
* Safe arming behavior: the controller must first be detected disarmed, then require a low-throttle arm transition.
* Live disarmed tuning and profile save/load for rates, PID, feedforward, motors, RC input, throttle, anti-gravity, and aerodynamic settings.
* FPV camera FOV and tilt settings are connected to the in-game camera.

### Telemetry and Validation

* Arm-triggered CSV telemetry with cleaner start/stop behavior and unique flight filenames.
* Telemetry includes motor output, thrust, acceleration, IMU-specific force, airspeed, dirty-air state, collision impulse, controller state, and flight state.
* Includes runtime, CSV-comparison, thrust-vector, vertical-response, and optional blackbox-replay audit paths.
* Designed to support comparison against real FPV logs instead of relying only on subjective tuning.

### Freestyle Environment and Performance

* Compact warehouse/bando environment with open entries, central atrium, catwalks, rooftop routes, exterior lines, and hollow dive towers into the building.
* Map visuals and collision are generated from shared batched geometry where possible to reduce node count while keeping collision aligned.
* Performance options include resolution scale, shadow settings, render distance, FPS cap, VSync, and 120/240 Hz simulation selection.

---

## Known Limits

This is not claiming 1:1 real-world flight behavior yet. The main systems are in place, but these areas still need continued controller testing and CSV validation:

* Final stick-to-angular-rate latency and feedforward/filter calibration.
* Exact propwash behavior during powerloops, dives, split-S moves, and catch-outs.
* Surface friction and restitution balance during fast wall rides, long skids, and sharp edge impacts.
* Extreme high-speed collision behavior.
* TX16S-specific HID mapping and switch behavior.
* Visual FPV camera feel and frame pacing on older Intel Macs.
* Additional macOS and Windows exported-build testing.
* Remaining map-seam, collision, and visual-cleanup passes as new issues are found.

---

## Run It

1. Open `project.godot` in **Godot 4.7**.
2. Calibrate your controller before serious flying.
3. Start disarmed, keep throttle low, then arm using a fresh switch transition.
4. Use **F1** for tuning, **F3** for diagnostics, **L** for telemetry(for devs), and **R** to reset.
5. For a basic project check, run `tests/RuntimeAudit.tscn` headlessly or from Godot.

See `HANDOFF.md` for the current technical state, validation notes, architecture, and future-work rules.

Check `LICENSE.md` for license information.
