# CLAUDE.md — moto-safety-node

## What this repo is

Safety monitor firmware running on **STM32G4** (G474 class, with FPU). Its ONE job: the **cornering safety decision** (Layer 1, deterministic, closed form: `v_max = √(µ·g·R)`, `tan(θ) = v²/(g·R)`) and driving the lean-angle LED ring. It decides independently even if rt-core, the Raspi, or the phone crash.

## What this repo is NOT

- EKF estimation is NOT here — lean angle, µ, and mass come from `moto-rt-core` over the platform CAN (E2E-protected). Do not recompute them.
- No ML. The ML output (Layer 3) can only pull the threshold toward the conservative side, never loosen the ceiling; if there's no ML data, the decision works exactly the same.
- NO other function is ever added here (that's the whole point of the isolation). If a suggestion comes in to "add this here too," reject it, ask the user.
- No engine/ECU intervention. Outputs are warnings only: LED, warning level on the platform CAN.

## Safety-critical rules

1. No dynamic memory, no recursion, no unbounded loops. Bare-metal super loop + timer interrupt (D-012); loop duration is measured and its budget documented.
2. All inputs are clipped to their physical range. Out-of-range or E2E `INVALID` (timeout/counter/CRC) → safe default. The exact behavior in this case is **open decision Q-002**: don't choose it yourself, ask the user.
3. rt-core's heartbeat is monitored. On loss, the node enters its own degraded mode and publishes this to CAN.
4. Listens to the vehicle bus in **silent (bus-monitoring) mode** only (wheel speed, etc.) and never transmits onto it. Publishes only on the platform bus.
5. The `safety-reviewer` agent is invoked after every change.

## Dependencies

`moto-vehicle-defs` → `external/moto-vehicle-defs` (tagged), only `gen/c/safety/` is included. Signals/IDs are never hand-written.

## Build

CMake + STM32CubeMX (HAL) + arm-none-eabi-gcc (D-007). Decision logic is pure C without a HAL dependency and is tested under `tests/host/` with Unity (boundary values, E2E error conditions).

## Context

`../moto-vehicle-defs/docs/ARCHITECTURE.md` §3-4, §7 · detail: `../moto-vehicle-defs/docs/hardware-architecture.md` §5b.2.
