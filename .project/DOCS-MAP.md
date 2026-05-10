# Docs Map

Pointers to everything beyond the hot-state files.

## Architectural
- `system-architecture.md` — pipeline design, VRAM budget, project structure, DSPy signature

## Hot state
- `CLAUDE.md` — agent boot sequence, lanes, invariants, behavioral rules
- `.project/STATE.md` — runtime, truths, blockers, milestones, recent history
- `.project/QUEUE.md` — packet table mirroring GitHub issues
- `.project/HANDOFF.md` — what matters now, next actions, do-not-do
- `.project/WORKSTREAMS.md` — lane-scoped memory (backend / frontend)
- `.project/RETRIEVAL.md` — CLI retrieval commands for agents
- `.project/DECISIONS.md` — settled decisions (D-001+)

## Project config
- `pyproject.toml` — Python dependencies (mlx, mlx-whisper, mlx-lm, dspy, fastapi, uvicorn, httpx)
- `.gitignore` — Python, Node/Tauri, MLX models, OS/IDE

## Backend structure
- `backend/core/` — STT engine (mlx-whisper), refiner engine (mlx-lm)
- `backend/logic/` — DSPy modules, context manager
- `backend/dispatchers/` — Hermes webhook client
- `backend/memory/global_rules.json` — persona, output format, constraints
- `backend/memory/projects/` — per-project context files (solyx.json as sample)

## External
- GitHub repo: `clubeedg-ship-it/voice-mission-control` (private)
- Hermes agents: remote (webhook URL TBD — needed by BACK.P6)
- Related project: `clubeedg-ship-it/gospot` (infrastructure, `.project/` conventions originated here)
