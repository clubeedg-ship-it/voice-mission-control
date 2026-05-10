# Docs Map

Pointers to everything beyond the hot-state files.

## Architectural
- `system-architecture.md` — pipeline design, VRAM budget, project structure, DSPy signature (not yet committed to repo)

## Hot state
- `CLAUDE.md` — agent boot sequence, lanes, invariants
- `.project/STATE.md` — runtime, truths, blockers, milestones, recent history
- `.project/QUEUE.md` — packet table mirroring GitHub issues
- `.project/HANDOFF.md` — what matters now, next actions, do-not-do
- `.project/WORKSTREAMS.md` — lane-scoped memory (backend / frontend)
- `.project/RETRIEVAL.md` — CLI retrieval commands for agents
- `.project/DECISIONS.md` — settled decisions (D-001+)

## External
- GitHub repo: `clubeedg-ship-it/voice-mission-control` (private)
- Hermes agents: remote (webhook URL TBD — needed by BACK.P6)
- Related project: `clubeedg-ship-it/gospot` (infrastructure, `.project/` conventions originated here)
