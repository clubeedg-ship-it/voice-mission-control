# DECISIONS

Settled decisions. Reference by ID (e.g. D-001) in other docs.

---

## D-001 — Local-only inference via MLX (2026-05-10)

**Context:** Choosing between cloud LLM APIs (faster to prototype, ongoing cost, latency from network round-trips) vs local inference on M4 Air.

**Decision:** All inference runs locally via MLX. Whisper Large V3 Turbo (FP16) for STT, Qwen 2.5-3B-Instruct (4-bit) for intent refinement. Combined VRAM ~3.4 GB + ~5 GB system = 8.4 GB, within 16 GB unified memory.

**Rationale:** Zero latency from network. Zero cost per request. Works offline. Voice interface must feel instant — cloud round-trips break the flow. The M4 Neural Engine + MLX makes this feasible at the model sizes needed.

**Consequences:** Model choices are constrained to what fits in ~8.5 GB. If a future model needs more VRAM, something else must shrink or the architecture needs a swap strategy.

---

## D-002 — Two lanes: backend + frontend (2026-05-10)

**Context:** Whether to define integration/IPC as its own lane.

**Decision:** Two lanes only — `BACKEND` and `FRONTEND`. The IPC contract (localhost HTTP, 16 kHz PCM audio in, JSON intent out) is documented as an invariant in CLAUDE.md, not managed as a separate workstream.

**Rationale:** Integration is the interface *between* the two lanes, not independent work. A third lane would create ownership ambiguity — who owns the API schema? With two lanes, the backend owns the API surface and the frontend consumes it.

**Consequences:** Backend must stabilize the sidecar API (BACK.P5) before frontend work starts. Changes to the IPC contract require updating CLAUDE.md invariants.
