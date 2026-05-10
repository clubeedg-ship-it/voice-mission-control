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

## Behavioral rules

These apply to every task. Bias caution over speed on non-trivial work.

### Think before coding
State assumptions explicitly. If uncertain, ask rather than guess.
If a simpler approach exists, say so before writing the complex one.
Stop when confused. Name what's unclear.

### Simplicity first
Minimum code that solves the problem. Nothing speculative.
No abstractions for single-use code — especially DSPy modules.
If a plain function does the job, don't wrap it in a class.

### Surgical changes
Touch only what the packet scopes. Don't "improve" adjacent files.
Editing stt_engine.py is not an invitation to refactor refiner_engine.py.
Match the style of the file you're in.

### Read before you write
Before adding code in a file, read its exports and its callers.
The pipeline stages (STT → context → refiner → dispatch) are tightly coupled.
Writing one stage without understanding what the previous stage outputs will break the chain.

### Surface conflicts, don't average them
If mlx-whisper and mlx-lm expect different patterns (imports, config, error handling),
pick one and flag the other. Don't blend two styles into a third.

### Checkpoint after every significant step
After each step in a multi-step task: summarize what's done, what's verified, what's left.
Don't continue from a state you can't describe back.
If you lose track, stop and restate.

### Match conventions once they exist
After BACK.P2, the codebase has conventions. Follow them.
If you think a convention is harmful, say so explicitly. Don't fork it silently.

### Fail loud
"Model loaded" is wrong if VRAM wasn't measured.
"Transcription works" is wrong if latency wasn't timed.
"Tests pass" is wrong if any were skipped.
Default to surfacing uncertainty, not hiding it.
