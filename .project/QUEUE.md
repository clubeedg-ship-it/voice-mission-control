# QUEUE

Packets map 1:1 to GitHub issues. Keep the `gh` column in sync.

| id | lane | gh | title | status | deps | next_action |
|---|---|---|---|---|---|---|
| SCAFFOLD.P0 | — | — | Scaffold .project/ framework + CLAUDE.md | done | none | closed 2026-05-10 — all .project/ files committed |
| BACK.P1 | backend | — | Python env + MLX model loading proof | todo | SCAFFOLD.P0 | create venv, install mlx-whisper + mlx-lm, verify both models load within VRAM budget |
| BACK.P2 | backend | — | Standalone STT proof (mic → transcript) | todo | BACK.P1 | record audio, run through mlx-whisper, print transcript, measure latency |
| BACK.P3 | backend | — | Refiner engine + DSPy IntentReification | todo | BACK.P2 | load Qwen 2.5-3B 4-bit, wire DSPy ChainOfThought, test with hardcoded transcripts |
| BACK.P4 | backend | — | Context manager + project switching | todo | BACK.P3 | keyword parser for "Project [Name]", JSON context loader, fallback to global |
| BACK.P5 | backend | — | FastAPI sidecar wrapping stages 1–4 | todo | BACK.P4 | /transcribe endpoint, model preloading on startup, localhost-only |
| BACK.P6 | backend | — | Hermes webhook dispatcher | todo | BACK.P5 | POST refined JSON to Hermes endpoint, error handling + retry policy |
| FRONT.P1 | frontend | — | Tauri app scaffold + sidecar config | todo | BACK.P5 | init Tauri v2 + React, configure Python sidecar spawn |
| FRONT.P2 | frontend | — | Recording overlay + global hotkey | todo | FRONT.P1 | hotkey activates overlay, captures 16kHz mono PCM, streams to sidecar |
| FRONT.P3 | frontend | — | Transcript + intent confirmation UI | todo | FRONT.P2 | display raw transcript, then refined intent, visual confirmation of dispatch |

Notes:
- Lane codes: `BACK` (backend), `FRONT` (frontend). `SCAFFOLD` is cross-lane.
- `gh` column filled when packet activates (issue created at start of work, not before).
- First feature branch cuts at BACK.P1.
