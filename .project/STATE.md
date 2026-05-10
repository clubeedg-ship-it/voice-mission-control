# STATE

repo: /Users/ottogen/voice-mission-control
branch: main
github: clubeedg-ship-it/voice-mission-control (private)
active_lane: backend
queue_head: BACK.P1 — Python env + MLX model loading proof

runtime:
  dev_machine: MacBook Air M4, 16 GB unified memory, macOS
  target_models:
    stt: Whisper Large V3 Turbo (FP16 via mlx-whisper, ~1.6 GB)
    refiner: Qwen 2.5-3B-Instruct (4-bit via mlx-lm, ~1.8 GB)
  vram_budget: 8.5 GB max (models + system/UI overhead ~5 GB)
  key_deps: mlx, mlx-whisper, mlx-lm, dspy, fastapi, tauri v2, react

truths:
  - Architecture doc drafted (system-architecture.md) — defines 4-stage pipeline: capture → context parse → intent reification → remote dispatch
  - CLAUDE.md committed 2026-05-10 — two lanes (BACKEND / FRONTEND), invariants locked
  - .project/ framework scaffolded 2026-05-10 — 7 files: STATE, QUEUE, HANDOFF, WORKSTREAMS, RETRIEVAL, DECISIONS, DOCS-MAP
  - All inference is local via MLX; no cloud LLM dependency (D-001)
  - Two lanes only: backend + frontend; IPC is an invariant, not a lane (D-002)
  - Hermes agents are remote; this system is the voice terminal only
  - Solo operator: user + Claude agents

blockers:
  - none (ready for BACK.P1)

focus:
  current_milestone: M0-SCAFFOLD (done) → M1-CORE next
  next_milestone: M1-CORE (standalone STT proof — Whisper via MLX, no UI)

milestones:
  M0-SCAFFOLD: docs, .project/ framework, repo discipline — DONE
  M1-CORE: standalone STT proof (mic → mlx-whisper → transcript printed)
  M2-REFINE: add Qwen refiner + DSPy IntentReification, context manager
  M3-BACKEND: FastAPI sidecar wrapping stages 1–4, Hermes dispatch
  M4-FRONTEND: Tauri app — recording overlay, hotkey, transcript + intent UI

retrieval:
  queue: .project/QUEUE.md
  handoff: .project/HANDOFF.md
  workstreams: .project/WORKSTREAMS.md
  decisions: .project/DECISIONS.md
  docs_map: .project/DOCS-MAP.md

recent:
  - 2026-05-10: SCAFFOLD.P0 done — CLAUDE.md + .project/ framework (STATE, QUEUE, HANDOFF, WORKSTREAMS, RETRIEVAL, DECISIONS with D-001/D-002, DOCS-MAP). 8 commits on main.
