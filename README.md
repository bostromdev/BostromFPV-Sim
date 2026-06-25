# BostromFPV-Sim-v0.2

This archive is a **complete replacement project**, not a patch. It includes `scripts/main.gd`, every scene, every required GDScript file, resources, data, and the Godot 4.7 `project.godot`.

**Import only this folder:** `BostromFPV-SIM/project.godot`. Do not copy it over an earlier partial patch folder.

## Godot 4.7 compile fix

Godot 4.7 rejects some implicit type inference from dictionary-backed data. The affected aerodynamic and force-integration values are explicitly typed in this build.

A lightweight, standalone FPV freestyle simulator prototype for Godot 4. It uses a deterministic, custom quad simulation written entirely in GDScript—no `RigidBody3D` flight model. The design target is an older Intel Mac first: stable controls, predictable frame pacing, and a low visual cost matter more than decorative graphics.

## What v0.2 Changes

- Factory Actual Rates are now **210 deg/s center sensitivity, 700 deg/s max rate, 0.54 expo** on **roll, pitch, and yaw**.
- `Safe 5-inch Default` restores those values.
- Existing `user://settings.json` files are migrated automatically **only when they exactly match the old shipped default**. Deliberately tuned rates are left alone.
- The README now distinguishes implemented features from items that still need hardware validation on the target MacBook Air and TX16S.

## Scope

Included in v0.2:

- Acro-only 5-inch freestyle quad with four independent motor models.
- Fixed 240 Hz internal flight-controller and motor steps, independent of render FPS.
- Custom state integration: no `RigidBody3D` flight model.
- Individual motor thrust, reaction torque, spool response, braking, dynamic-idle-inspired minimum RPM, gravity, drag, battery sag, ground effect, and deterministic propwash approximation.
- Controller-style Rate PID with P/I/D/FF debug terms, I-term relax, anti-gravity, D filtering, feedforward limits, and Actual Rates stick curves.
- Generic HID controller mapping and a TX16S calibration screen.
- Keyboard fallback, FPV and chase cameras, dark blue OSD/debug UI, a tuning screen, CSV telemetry, and a primitive-only low-poly hangar.

Intentionally out of scope: multiplayer, procedural maps, iPhone scans, AI pilots, missions, leaderboards, HD video effects, persistent damage modeling, and photoreal graphics.

## Maps

- **Freestyle Arena (Performance)** is the default lightweight warehouse map. It remains the performance-oriented option for older computers and is unchanged.
- **Arena Revamped (Optimized Detail)** is a separate duplicate of FreestyleArena with a wider opaque material/value hierarchy for depth, a rear/right 118 m L-shaped landmark, sparse visual-only roof and exterior guide grids, and a 4.0 m square launch pad that raises the start point ~1.524 m (5 ft) with clearance for the quad's prop/arm bounds at reset. It does not replace the default map.
- The landmark carries a non-colliding BostromFPV sign (B-and-arrow mark + BOSTROM/FPV wordmark) near its upper roofline, facing the launch pad, plus **four fly-through window openings** — two stacked on each of its west and rear faces — that are genuinely passable (collision is cut inside each opening; the frame stays solid). FreestyleArena is unchanged.
- The menus, HUD, and overlays share a unified BostromFPV brand theme (dark aerospace blue/silver); this is purely cosmetic and changes no controls, settings, or flight behavior.
- Arena Revamped keeps its guide geometry and branding batched, opaque, and **non-colliding**, so the underlying roof/ground collision is untouched and sliding stays smooth. The landmark and spawn pedestal are solid static collision; no global renderer or project setting changes are required.
- The **Arena detail** graphics control (and the Performance preset) skips Arena Revamped's visual-only grids/branding for a lighter primitive count. It never removes structural collision, map routes, or spawn.

## Coordinate Convention

- **World Y** is up.
- Drone **local forward** is **-Z**.
- Drone **local right** is **+X**.
- Drone **local up / thrust** is **+Y**.
- Angular velocity is stored in local body axes: pitch X, yaw Y, roll Z.

## Run on Mac

1. Open `project.godot` in a Godot 4 build that runs on your MacBook Air.
2. Select the **Compatibility** renderer. Do not switch this project to Forward+ or Mobile.
3. Run with **F6/F5**; the game starts at the main menu.
4. Begin at **Graphics → Balanced (Recommended)** preset (0.85 resolution scale, low shadows, medium distance). Optionally set an FPS cap of **60**.
5. If frame pacing is not stable, switch to the **Performance** preset (0.70 scale, shadows off, low distance, reduced Arena Revamped detail) before relying on VSync or a 60 FPS cap.

No external assets, plugins, Python, C#, .NET, native libraries, or online services are required.

## Controls

### TX16S / HID expected layout

- Roll: right-stick horizontal
- Pitch: right-stick vertical
- Throttle: left-stick vertical
- Yaw: left-stick horizontal
- Arm: an assigned switch/button

USB axis numbering varies by EdgeTX model and profile. The shipped mapping is only a starting point: complete **Controller Setup** before flying.

### Keyboard fallback

- **W / S**: raise / lower throttle
- **U / O**: roll left / right
- **I / K**: pitch forward / back
- **A / D**: yaw left / right
- **Space**: toggle keyboard arm switch
- **X**: disarm
- **R**: reset the drone
- **C**: FPV / chase camera
- **F1**: tuning panel while disarmed
- **F3**: debug overlay
- **L**: telemetry logging
- **Esc**: return to the main menu; closes tuning first

The quad starts disarmed. Arming requires throttle below 5%, no active crash state, and a fresh disarmed-to-armed input transition. Reset and crash recovery cannot re-arm from a switch that stayed active.

## How to Calibrate TX16S

1. Connect the TX16S and select **USB Joystick / HID** on the radio.
2. Choose **Controller Setup** in the sim.
3. Click **Refresh**, select the TX16S, then capture roll, pitch, throttle, and yaw one at a time.
4. Move the requested stick cleanly through at least one-third of travel; the calibration page selects the axis with the largest movement.
5. Capture the arm switch/button. The setup screen supports switches exposed by EdgeTX as either joystick buttons or joystick axes.
6. Center roll, pitch, and yaw sticks, then use the three **Center** buttons.
7. Put throttle fully low and use **Set Throttle Min**; then go fully high and use **Set Throttle Max**.
8. Confirm each live channel moves in the intended direction. Use each invert control only when needed.
9. Scroll to the bottom if needed, then click **Save Mapping**. It writes `user://controller_mapping.json` and loads automatically next launch.

Deadband is entered directly as a percentage in 0.1% steps. For example, `1.5%` removes the first 1.5% of stick movement around center; it does not mean `15%`.

## Default 5-inch Profile

`Mark 4 5in Freestyle 6S`

- 225 mm wheelbase; 0.1125 m arm length
- 0.68 kg ready-to-fly mass
- RushFPV Blade F7 flight controller
- SEQURE 6S 65A AM32 4-in-1 ESC with 8–128 kHz PWM capability
- EMAX 2306 1700 KV motors on 6S 1800 mAh
- SpeedyBee TX800 5.8 GHz / 800 mW analog VTX
- 915 MHz ExpressLRS receiver with T antenna
- 4.9 × 3.4 × 3 (4934) tri-blade target
- Estimated normalized 4934 thrust/current curve; it is not claimed as measured bench data
- 12.5 N configured maximum static thrust per motor at 4.20 V/cell
- 25° camera tilt; 115° FPV FOV for stronger depth perception
- **Actual Rates: 210 center / 700 max / 0.54 expo on roll, pitch, and yaw**

The motor response uses a configurable first-order spool model. Thrust is calculated at each motor from `k_thrust × omega²`; roll/pitch torque comes from motor force at each arm location, while alternating motor reaction torque creates yaw control.

Motor order is front-right, rear-right, rear-left, front-left. The default rotation is always props-out: front-right and rear-left CCW, rear-right and front-left CW when viewed from above.

An earlier internal flight-model pass added a calibrated nonlinear thrust curve, asymmetric motor rise/braking, measured X-frame inertia, quadratic body and angular drag, anti-windup rate PID, descent/recovery-driven propwash, and subtle deterministic FPV camera vibration.

Later internal tuning passes added model-based rate feedforward, independent prop reaction torque for faster props-out yaw, narrower dirty-air detection, softer maximum thrust, and revised damping.

The tuning screen includes clean-room Flight Controller and ESC & Motors sections. P/I/D/FF use familiar integer ranges but are converted into simulator torque units. ESC protocol, PWM frequency, motor timing, demag compensation, startup/ramp power, active braking, and RPM telemetry are configurable and affect the motor model. Motor direction is locked to Props Out. Actual Rates remains the only rate format.

The dashboard groups tuning into Feedforward, I-Term Relax, Throttle & Motors, Anti Gravity, and RC Link panels. The controls change setpoint prediction, integrator relaxation, throttle transients, motor limiting and idle RPM, voltage-sag compensation, thrust linearization, independent yaw deadband, RC smoothing, and setpoint/throttle filtering.

## Tuning the Default Profile

The tuning screen accepts familiar integer P/I/D/FF values and maps them into the simulator's physical torque model. Actual Rates is the only available rate format.

1. **Start with the supplied rates:** 210 center / 700 max / 0.54 expo. This is the baseline for all three axes.
2. **Set rates before PID.** Center sensitivity sets near-center feel; maximum rate sets full-stick flip/roll speed; expo softens the middle. The tuning graph shows the active curve.
3. **Use P for initial tracking.** Raise slowly until response is sharp but not visibly oscillatory, then back off.
4. **Use D for stopping.** Small increases tighten stopping and reduce bounce-back. Too much D makes the simulated motor response feel harsh or overdamped.
5. **Use I for disturbance recovery.** Raise only enough for reliable recovery; I-term relax and anti-gravity already control buildup during hard stick and throttle changes.
6. **Use FF for stick immediacy.** Add a small amount for crisper flips. Reduce FF or raise smoothing if end-of-flip overshoot is harsh.
7. **Set dynamic idle last.** Lower values feel freer at zero throttle; higher values improve stopping/recovery and inverted-drop authority.
8. **Change one family at a time:** rates → P/D → I → FF → motor response/dynamic idle.

Tuning edits apply live only while disarmed. **Save Profile** writes `user://settings.json`; **Load Profile** rereads it; **Reset Profile** and **Safe 5-inch Default** restore the full factory settings profile, including the 210 / 700 / 0.54 rates.

## Settings and Profiles

- Factory defaults: `res://data/default_settings.json`
- Saved user settings: `user://settings.json`
- Factory controller mapping: `res://data/controller_mapping.json`
- Saved controller mapping: `user://controller_mapping.json`
- Factory rate resource label: `res://resources/default_rates_profile.tres`

The project loads factory settings first, then overlays a saved user profile. In v0.2, only the original shipped rate preset is automatically migrated to the new default. Custom rates are not overwritten.

## Simulation Timing

- Rendering: target 60 FPS; 30 FPS should remain controllable.
- Godot physics: `120` ticks per second.
- Flight controller / motor simulation: fixed `240 Hz`, `1/240 s` per step.
- Catch-up cap: 12 fixed internal steps per Godot physics update, followed by one bounded coarse recovery step.
- Any time still beyond that bounded recovery is dropped and counted in F3 diagnostics to protect input responsiveness.

## Architecture

| File | Responsibility |
|---|---|
| `drone_physics.gd` | `DroneState`, 240 Hz custom integration, force/torque accumulation, gyro model, PID/mixer linkage, battery/aero integration |
| `motor_model.gd` | First-order ESC/motor response, RPM, thrust, reaction torque, current draw |
| `battery_model.gd` | 6S open-circuit curve, capacity drain, internal-resistance sag, voltage floor |
| `aerodynamic_model.gd` | Quadratic body-axis drag, deterministic propwash torque, ground effect, optional gusts |
| `pid_controller.gd` | Clean-room Rate PID per axis with P/I/D/FF, D LPF, I relax, anti-gravity, and FF limits |
| `rate_curve.gd` | Actual-Rates-inspired stick-to-rate curve |
| `mixer.gd` | Quad-X mixing and authority-preserving desaturation |
| `input_manager.gd` | HID/keyboard controls, deadband, throttle and arm handling |
| `controller_calibration.gd` | HID detection, axis/button capture, center/throttle calibration, mapping save |
| `drone_controller.gd` | Fixed-step accumulator, collision/crash detection, visual drone, telemetry wiring |
| `test_hangar.gd` | Compact single-building construction bando with shafts, floors, gaps, roof lines, and an attached crane |
| `flight_scene.gd` | Map, drone, cameras, HUD, in-flight tuning lifecycle |
| `osd_hud.gd` | OSD and F3 diagnostics |
| `tuning_manager.gd` | Editable Drone/Rates/PID UI and custom-drawn rate graph |
| `telemetry_logger.gd` | Buffered 120 Hz CSV output |
| `settings_manager.gd` | Default/user JSON loading, safe legacy-rate migration, controller mapping, graphics application |

## Telemetry

Telemetry is off by default. Toggle it in the menu or with **L** in flight. Output is buffered and flushed periodically instead of writing every 240 Hz physics step.

When telemetry is enabled, logging starts on a valid arm event and stops on disarm, crash, reset, or scene exit. Runtime logs now save to `/Users/grok/Desktop/simlogs/` so the project stays clean. The simulator creates that folder automatically; it falls back to `user://telemetry/` only if the Desktop location cannot be used.

The CSV includes time, position, velocity, Euler attitude, roll/pitch/yaw rate and setpoint, P/I/D/FF terms, motor command/RPM, voltage/current/mAh, throttle, propwash, ground effect, explicit world acceleration, local IMU specific force, world thrust/drag, horizontal/forward speed, and average motor command/RPM.

Development-only audits, calibration references, historical simulator logs, and the active AI handoff are in the sibling `../Agent_Review/` folder.

## Graphics and Performance Target

The project uses:

- Compatibility renderer / OpenGL: `renderer/rendering_method="gl_compatibility"`
- `physics/common/physics_ticks_per_second=120`
- 1280 × 720 logical viewport
- Quality presets: Performance, Balanced (Recommended), High Detail, Custom. Changing a preset-governed control (resolution scale, shadows, render distance, Arena detail) switches the preset to Custom; **Restore Recommended** reapplies Balanced.
- Resolution scales: 0.70, 0.85, 1.00
- Shadows: off or low; one directional shadow-casting light maximum
- FPS cap: 60, 90, 120, 144, 165, 240, or Unlimited (render-only; it never changes the 120/240 Hz simulation rate)
- VSync control; physics-simulation rate is a separate control
- The FXAA control is removed because it is a no-op under the GL Compatibility renderer.

The map is a compact 58 × 58 m drawing-board site focused on one 25 m-tall construction bando. Alternating partial floors, a vertical shaft, wall fragments, framed gaps, a split roof, and an attached crane create dense freestyle lines and strong depth cues. It remains primitive-only with simple static collisions for Intel HD 5000-class graphics.

## Exact Project Settings

`project.godot` uses:

```ini
run/main_scene="res://scenes/Main.tscn"
config/features=PackedStringArray("4.0", "GL Compatibility")
renderer/rendering_method="gl_compatibility"
renderer/rendering_method.mobile="gl_compatibility"
window/size/viewport_width=1280
window/size/viewport_height=720
physics/common/physics_ticks_per_second=120
physics/common/physics_jitter_fix=0.0
```

`AppSettings` is autoloaded from `res://scripts/settings_manager.gd`; it registers the keyboard actions at startup.

## Known Limitations

- This is a **physically informed simulator**, not a certified aerodynamic model.
- Propwash is a limited deterministic approximation, not CFD.
- Prop aerodynamic curves, motor data, current draw, and drag are configurable coefficient-based approximations.
- Controller-style PID values are mapped into simulator units; exact behavior still depends on the simulated frame, motors, props, battery, and ESC profile.
- Collision uses a cheap swept broad phase followed by precise oriented zones for all four prop discs, arms, motor bells, battery, FPV camera, and main frame. Contact response uses point velocity, angular inertia, friction, and component-specific crash thresholds.
- The next milestone is calibration against real DVR footage and Blackbox logs from a known 5-inch build.
- Multiplayer, map scanning, extra quads, HD video effects, and persistent damage modeling remain out of scope for v0.2. The flight drone now uses swept component collision zones for its props, arms, motor bells, battery, camera, and main frame.

## Acceptance Status

| Requirement | Status |
|---|---|
| Project files, scenes, scripts, resources, and data are present | Statically verified in the delivery package. |
| Main menu and Fly scene paths | Parsed and launched headlessly with Godot 4.7. |
| Keyboard arm/disarm/reset safety | Automated low-to-high arm and reset-lockout checks pass; interactive feel still needs local testing. |
| TX16S USB calibration | Implemented as generic HID axis/button capture; validate axis layout with the actual radio. |
| Custom 240 Hz sim / no `RigidBody3D` | Implemented. |
| Four motors, inertia, mixer, battery sag | Implemented. |
| PID, rates, FF, anti-gravity, dynamic idle | Implemented. |
| Propwash and ground effect | Implemented as configurable deterministic approximations. |
| FPV/chase camera, OSD, debug HUD | Implemented. |
| Tuning UI and buffered CSV telemetry | Implemented. |
| Primitive-only test hangar | Implemented. |
| Intel HD 5000 60 FPS target | Designed for the target; measure on the MacBook Air and reduce scale first if needed. |
| Final runtime / hardware validation | Godot 4.7 headless runtime audit passes; TX16S and Intel Mac frame pacing still require hands-on testing. |

## Suggested First Test

1. Start in the hangar with keyboard fallback.
2. Arm at zero throttle, lift gently, then make small roll, pitch, and yaw inputs.
3. Press F3 and confirm requested rates, motor commands, RPM, voltage, and overload values change sensibly.
4. Press L and confirm a CSV appears in `/Users/grok/Desktop/simlogs/`.
5. Calibrate the TX16S, verify all channel directions, then fly the default **210 / 700 / 0.54** rates.


## Godot 4.7 compatibility

Dictionary-backed aerodynamic and force-integration values use explicit `Vector3` annotations. The project declares `config_version=5`.

---

## Project history and current handoff

See `../Agent_Review/HANDOFF.md` for the compact current continuation guide. Use `../Agent_Review/GRAPHICS_BASELINE.md` for graphics/performance work and `../Agent_Review/HANDOFF_v0.1.1.md` only for targeted historical reference.

## TX16S / Mac HID control note

The previous default controller map did not match the axis order reported by an OpenTX RadioMaster TX16S on this Mac. This build adds **Auto Map TX16S** on the Controller Setup screen.

For the device reported as `OpenTX RadioMaster TX16S Joystick`, the auto map is:

| Function | Godot HID axis |
|---|---:|
| Throttle | A0 |
| Yaw | A1 |
| Roll | A2 |
| Pitch | A3 |

It also sets throttle low correctly: with the throttle stick physically down and A0 reading about `-1.000`, the simulator sees **0% throttle**, rather than full throttle. Arm is deliberately left unassigned until you capture your chosen TX16S arm switch.

### Mapping steps

1. Open **Controller Setup** from the main menu.
2. Confirm the connected controller says `OpenTX RadioMaster TX16S Joystick`.
3. Press **Auto Map TX16S**.
4. Press **Capture Arm Switch**, then flip only the switch you use for arm.
5. Leave the sticks centered and press **Center Roll**, **Center Pitch**, and **Center Yaw**.
6. Hold throttle fully down and press **Set Throttle Min**. Hold it fully up and press **Set Throttle Max**.
7. Set **Stick / throttle-low deadband** to `0.020` and press **Save Mapping**.
8. Close the panel, then select **Fly** from the main menu.

Small idle readings such as `A1 -0.001`, `A2 -0.003`, and `A3 +0.005` are normal HID noise. A deadband of `0.020` removes them with room to spare while keeping the sticks responsive. Use `0.010` only if you want a tighter center and your radio is clean; use `0.030` if a stick still drifts in the simulator.
