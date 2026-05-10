# HANDOFF

Current mode:
- router/orchestrator; solo operator (user + Claude agents)
- **Greenfield.** Repo scaffolded 2026-05-10, no code yet.
- Architecture doc defines the full pipeline; `.project/` framework mirrors gospot conventions.

What matters now:
- M0-SCAFFOLD is wrapping up — `.project/` files landing now.
- Next work is BACK.P1: set up the Python environment, install `mlx-whisper` and `mlx-lm`, and prove both models load on the M4 Air within the 8.5 GB VRAM budget.
- Everything before BACK.P5 is standalone Python scripts — no FastAPI, no Tauri, no UI. Keep it minimal and prove each piece works in isolation.

Recommended next actions (priority order):
1. **BACK.P1 — Python env + model loading proof.** Create venv, `pip install mlx-whisper mlx-lm`, load Whisper Large V3 Turbo + Qwen 2.5-3B 4-bit, measure combined VRAM. This is the single highest-risk item — if the models don't fit, the architecture needs to change.
2. **BACK.P2 — Standalone STT proof.** Record from mic, feed to mlx-whisper, print transcript. Measure latency against the <500ms target. Test English and Portuguese.
3. **BACK.P3 — Refiner + DSPy.** Wire up `IntentReification` signature with hardcoded transcripts and a sample project context JSON. Validate structured output.

Key decisions still open:
- Hermes webhook shape (URL, auth, payload schema) — needed by BACK.P6 but not blocking until then.
- Tauri sidecar strategy (spawn FastAPI as child process vs Tauri IPC) — needed by FRONT.P1 but not blocking backend work.

Do not do:
- Do not add cloud LLM fallbacks — local-only is an invariant.
- Do not start Tauri/frontend work until BACK.P5 is done (the sidecar API must exist first).
- Do not create GitHub issues for packets until work starts on them.
- Do not mix lanes in a single packet.
