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

---

## D-003 — Native macOS app via Tauri, no containerization (2026-05-10)

**Context:** Whether to containerize the application or run natively.

**Decision:** No containers. The application runs natively on macOS as a Tauri `.app` bundle. The Python sidecar (FastAPI + MLX models) is bundled inside the `.app` package. The final product is a single application the user finds via Spotlight, launches from the dock, and never thinks about dependencies.

**Rationale:** MLX requires Metal (Apple's GPU framework) which is unavailable inside Docker on macOS — Docker Desktop runs a Linux VM, so Metal, the Neural Engine, and hardware microphone access are all cut off. Containerization would break the three pillars of the architecture: local inference, low-latency audio capture, and native OS integration (global hotkey, overlay window). Tauri's bundling already provides the isolation containers would give — the `.app` contains the frontend, the Rust shell, and the Python sidecar with a vendored venv.

**Consequences:** No Docker, no docker-compose, no Dockerfile in this repo. Development setup is `python -m venv .venv && pip install -e .` on the host machine. The coding assistant must not introduce container-based workflows. VRAM measurement must use `sudo powermetrics --samplers gpu_power` or Activity Monitor, not container-level metrics. Distribution is a `.app` bundle built by `tauri build`, not a container image.
