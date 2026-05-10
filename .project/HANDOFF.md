# HANDOFF

Current mode:
- router/orchestrator; solo operator (user + Claude agents)
- **Scaffolding complete.** Repo has docs, package structure, dependencies — no implementation code yet.
- Architecture doc + `.project/` framework + behavioral rules all in place.

Dev setup (run once):
```bash
cd /Users/ottogen/voice-mission-control
python -m venv .venv
source .venv/bin/activate
pip install -e .
```

What matters now:
- M0-SCAFFOLD is done. M1-CORE starts with BACK.P1.
- This is a native macOS app (D-003) — no Docker, no containers. MLX needs Metal directly on the host.
- Everything before BACK.P5 is standalone Python scripts — no FastAPI, no Tauri, no UI. Prove each piece in isolation.
- VRAM budget is the single biggest risk. If Whisper + Qwen don't fit in 8.5 GB, the architecture changes.

Recommended next actions (priority order):
1. **BACK.P1 — Python env + model loading proof.** Load Whisper Large V3 Turbo + Qwen 2.5-3B 4-bit via MLX. Measure VRAM with `sudo powermetrics --samplers gpu_power -i 1000`. This is the gate — everything downstream depends on it.
2. **BACK.P2 — Standalone STT proof.** Record from mic, feed to mlx-whisper, print transcript. Measure latency against the <500ms target. Test English and Portuguese.
3. **BACK.P3 — Refiner + DSPy.** Wire up `IntentReification` signature with hardcoded transcripts and `backend/memory/projects/solyx.json`. Validate structured JSON output matches the schema in `backend/memory/global_rules.json`.

Key decisions still open:
- Hermes webhook shape (URL, auth, payload schema) — needed by BACK.P6 but not blocking until then.
- Tauri sidecar bundling strategy — needed by FRONT.P1 but not blocking backend work.

Do not do:
- Do not containerize anything — MLX requires Metal on bare macOS (D-003).
- Do not add cloud LLM fallbacks — local-only is an invariant (D-001).
- Do not start Tauri/frontend work until BACK.P5 is done.
- Do not create GitHub issues for packets until work starts on them.
- Do not mix lanes in a single packet.
