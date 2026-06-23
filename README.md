# Bostrom FPV Sim v0.1.1

**Bostrom FPV Sim** is a Godot 4.7 freestyle FPV simulator built around a custom quadcopter flight model—not a stock `RigidBody3D` with arcade controls.

v0.1.0 was the proof of concept: a simple drone that could arm, take input, and fly.

v0.1.1 is the point where it turned into a real simulator project: custom fixed-step flight physics, per-motor response, FPV-style rate control, aerodynamic effects, controller calibration, data logging, collision physics, and a purpose-built freestyle warehouse. It is still early-access software, but there is a lot more under the hood than a basic “flying camera.”

## What changed from v0.1.0

### Flight model: from simple flight to a modeled quad

- Custom deterministic flight simulation at **240 Hz** (with a **120 Hz low-spec mode**) rather than relying on Godot’s default rigid-body behavior.
- Individual motor/ESC response with spool-up, braking, RPM, thrust, current draw, a 5-inch motor/prop curve, and battery-voltage sag.
- Quad-X motor mixing, body inertia, gravity, directional thrust, body/rotor drag, dynamic idle, throttle response, and motor desaturation.
- Betaflight-inspired **Actual Rates**, PID, feedforward, I-term relax, anti-gravity, RC smoothing, setpoint filtering, throttle filtering, deadband, and thrust-linearization controls.
- Default rate baseline: **210 center sensitivity / 700 max rate / 0.54 expo** on roll, pitch, and yaw.

### Air and momentum

- Added ground effect, aerodynamic drag, propwash/dirty-air approximation, wake re-entry, turbulence, authority loss, and smooth recovery.
- Reduced overly strong passive drag and damping so throttle cuts can stay ballistic instead of feeling like the drone is being air-braked by a game engine.
- Added deterministic thrust-vector and vertical-response audits so camera tilt cannot accidentally change physical thrust direction.

### Collision: no more fake “crash state” behavior

- Replaced simple collision behavior with swept component-level checks for the frame, arms, motors, prop discs, battery, and camera.
- Collisions use contact point, normal, relative contact velocity, mass, inertia, friction, and restitution to create a real impulse and off-center rotation.
- Normal impacts do **not** zero momentum, auto-level the quad, force a fake settle, or automatically disarm it. The pilot keeps control unless the drone leaves world bounds.
- Added overlap recovery/depenetration and collision-event filtering so resting contact and tiny scrapes do not spam telemetry every frame.

### Controls, safety, and tuning

- Replaced hard-coded transmitter assumptions with guided generic HID calibration for roll, pitch, throttle, yaw, endpoints, direction, and arming.
- Added safe arm sequencing: the radio must first be seen disarmed, and a low-to-high arm transition is required at low throttle.
- Added live disarmed tuning and profile save/load for rates, PID, feedforward, motors, RC input, throttle, anti-gravity, and aerodynamic settings.
- Wired FPV camera FOV and tilt settings to the actual camera.

### Telemetry and validation

- Added arm-triggered flight CSV telemetry, collision logging, unique filenames, and cleaner start/stop behavior.
- Added telemetry for motor output, thrust, acceleration, IMU-specific force, airspeed, dirty-air state, collision impulse, and controller/flight state.
- Added headless Runtime, CSV-comparison, thrust-vector, vertical-response, and optional blackbox-replay audits.
- Includes reference-log and aircraft-profile paths so simulator behavior can be compared against real FPV flight data instead of tuned purely by guesswork.

### Freestyle environment and performance

- Rebuilt the map into a compact warehouse/bando with open entries, a central atrium, catwalks, rooftop routes, exterior lines, and **four hollow dive towers** that drop into the building.
- Map visuals and collision are generated from the same batched geometry, reducing node count while keeping collision aligned with the visible course.
- Added performance options for older hardware: resolution scale, low shadows, render distance, FPS cap, VSync, and 120/240 Hz simulation selection.

## Problems that drove this release

This update came from real problems noticed while flying the prototype—not a feature checklist.

- **Arcade / floaty feel:** v0.1.0 did not preserve momentum, throttle-cut behavior, braking, or rotation the way a real freestyle quad should. v0.1.1 adds the underlying motor, inertia, drag, and thrust systems needed to tune that honestly.
- **“Jello,” loose response, and delayed stick feel:** the simulator gained explicit RC smoothing, setpoint, feedforward, and throttle-filter controls plus telemetry paths so the source can be separated from PID tuning, input timing, or visuals. Fine latency calibration still needs real-stick testing.
- **Wall rides and crashes felt wrong:** collisions could jerk the quad, over-stop it, or make it feel like a single unbreakable box. v0.1.1 moves to component-aware contact and physical impulses, but aggressive wall rides and grazing seams still need hands-on validation.
- **Throttle cuts, powerloops, split-S moves, and inverted recovery were not believable enough:** passive damping was reduced, motor braking/dynamic idle were modeled, and dirty-air/propwash behavior was added. The model is deliberately conservative until more flight logs confirm the tuning.
- **Controller setup could be unreliable:** axis order and arm behavior were too assumption-heavy. Calibration and arm safety are now built into the project.
- **Telemetry was not useful enough:** v0.1.1 logs only meaningful armed flights and records the physics/controller data needed to compare simulator response with real flight logs.
- **The course was too primitive:** the project now has a compact, optimized freestyle space instead of a basic test scene.

## Known limits

This is not claiming 1:1 real-world flight behavior yet. The core systems are in place, but the following still require real controller flights and CSV comparison:

- final stick-to-angular-rate latency and feedforward/filter tuning;
- exact propwash strength during powerloops, split-S moves, dives, and catch-outs;
- friction/restitution balance on hard edges, tower seams, and long skids;
- extreme collision behavior at full speed;
- TX16S-specific HID mapping and switch behavior;
- visual FPV camera feel and frame pacing on older Intel Macs;
- macOS and Windows exported-build testing.

## Run it

1. Open `project.godot` in **Godot 4.7**.
2. Calibrate the controller before serious flying.
3. Start disarmed, keep throttle low, then arm with a fresh switch transition.
4. Use **F1** for tuning, **F3** for diagnostics, **L** for telemetry, and **R** to reset.
5. For a basic project check, run `tests/RuntimeAudit.tscn` headlessly or from Godot.

See `HANDOFF.md` for the detailed technical state, validation notes, architecture, and future-work rules.

Check `LICENSE.md` for license information.
