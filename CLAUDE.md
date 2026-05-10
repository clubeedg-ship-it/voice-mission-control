# voice-mission-control runtime note

- Work only in `/Users/ottogen/voice-mission-control`
- Read `.project/STATE.md`, `.project/QUEUE.md`, `.project/HANDOFF.md` first
- Then read the relevant lane section in `.project/WORKSTREAMS.md`
- Read `.project/DECISIONS.md` only if the task touches a prior decision

## What this is

Local voice-to-agent interface for M4 Air (16GB). Captures speech, transcribes
via MLX-Whisper, refines intent with a local LLM (Qwen 2.5-3B 4-bit via MLX),
and dispatches structured commands to remote Hermes agents via webhook.

## Lanes

- `BACKEND` — Python sidecar: STT engine, refiner LLM, DSPy modules, context
  manager, Hermes dispatcher. All inference is local via MLX.
- `FRONTEND` — Tauri app (React): recording overlay, hotkey capture, transcript
  display, intent confirmation UI.

## Invariants

- Total VRAM footprint ≤ 8.5 GB (Whisper ~1.6 GB + Qwen ~1.8 GB + system ~5 GB)
- All inference local — no cloud LLM calls in the pipeline
- Models stay resident in memory after startup; no cold-load per request
- Frontend↔backend IPC: frontend spawns FastAPI sidecar, communicates over localhost HTTP
- Audio format: 16 kHz mono PCM (Whisper's expected input)
- STT latency target < 500ms for 10s clips; refinement < 1.2s
- Project contexts are siloed — LLM only sees the active project file
- One lane per packet; do not mix frontend and backend work

## Retrieval

- Prefer CLI retrieval over repo-wide searching
- GitHub issues are authoritative for packets; `.project/QUEUE.md` mirrors them
