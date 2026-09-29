# moto-safety-node

Part of [moto-platform](https://github.com/moto-platform), an SDV-style diagnostics, telemetry and rider-assistance platform for motorcycles (first vehicle: Honda CL250).

Safety monitor firmware running on **STM32G4** (G474 class, with FPU). Its ONE job: the **cornering safety decision** (Layer 1, deterministic, closed form: `v_max = √(µ·g·R)`, `tan(θ) = v²/(g·R)`) and driving the lean-angle LED ring. It decides independently even if rt-core, the Raspi, or the phone crash.

**Status:** skeleton, no code yet. The build system, tests and CI are added by `/repo-bootstrap moto-safety-node` when work on this repo starts (setup order: `moto-vehicle-defs/docs/ARCHITECTURE.md` §9).

- Architecture and decisions: [moto-vehicle-defs/docs](https://github.com/moto-platform/moto-vehicle-defs/tree/main/docs) (`ARCHITECTURE.md`, `DECISIONS.md`)
- Signals, CAN IDs and DIDs come only from [moto-vehicle-defs](https://github.com/moto-platform/moto-vehicle-defs) (git submodule pinned to a tag)
- Scope rules for contributors and Claude Code: [`CLAUDE.md`](CLAUDE.md)

## License

MIT, see [LICENSE](LICENSE) (D-036).
