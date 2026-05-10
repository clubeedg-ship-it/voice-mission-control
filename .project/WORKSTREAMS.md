# voice-mission-control Lane Memory

Use one lane at a time. Read only the lane you are working in.

## Backend lane

### Scope
- Primary roots:
  - `backend/` (Python sidecar: FastAPI, MLX engines, DSPy modules, dispatchers)
  - `backend/memory/` (project context files + global rules)
- Treat the Tauri frontend as a downstream consumer.

### Search order
1. `.project/STATE.md`, `.project/QUEUE.md`
2. this lane section
3. `CLAUDE.md`
4. architecture doc (`system-architecture.md`)
5. smallest relevant source file

### Search map (will grow)
- STT engine, Whisper config, audio format → `backend/core/stt_engine.py`
- Refiner LLM, Qwen config, MLX-LM → `backend/core/refiner_engine.py`
- DSPy signatures, IntentReification → `backend/logic/dspy_modules.py`
- Context switching, keyword parser, project JSON → `backend/logic/context_manager.py`
- Hermes dispatch, webhook, retry → `backend/dispatchers/hermes_client.py`
- FastAPI routes, sidecar entry point → `backend/main.py`

### Delivery contract
- Every backend packet must be testable standalone (script or pytest)
- Model loading and inference must stay within the 8.5 GB VRAM budget
- No cloud LLM calls — all inference via MLX
- Audio input is always 16 kHz mono PCM
- Structured output from the refiner must be valid JSON with `action`, `parameters`, `context_summary`

---

## Frontend lane

### Scope
- Primary roots:
  - `frontend/` (Tauri v2 + React app)
- Treat the backend sidecar API as a fixed reference.

### Search order
1. `.project/STATE.md`, `.project/QUEUE.md`
2. this lane section
3. `CLAUDE.md`
4. architecture doc (`system-architecture.md`)
5. smallest relevant component or config file

### Search map (will grow)
- Tauri config, sidecar spawn, IPC → `frontend/src-tauri/`
- Recording overlay, hotkey binding → `frontend/src/components/`
- Transcript display, intent confirmation → `frontend/src/components/`
- App state, API calls to sidecar → `frontend/src/hooks/` or `frontend/src/lib/`

### Delivery contract
- Frontend work does not start until the backend sidecar API exists (BACK.P5 done)
- Audio capture happens in the frontend; raw PCM is sent to the sidecar over localhost HTTP
- UI displays two states: raw transcript (streaming) and refined intent (final confirmation)
- No direct model calls from the frontend — all inference is delegated to the sidecar

---

## References

- `CLAUDE.md` for invariants and lane definitions
- `system-architecture.md` for pipeline design
- `.project/DECISIONS.md` for settled rules
- `.project/QUEUE.md` for sequence state
