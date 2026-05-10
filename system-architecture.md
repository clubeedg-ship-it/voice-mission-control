# System Architecture: Voice-to-Agent Mission Control

This architecture defines a local, high-fidelity voice interface optimized for the **M4 Air (16GB RAM)**. It leverages **MLX** for hardware-accelerated inference and **DSPy** for programmatic intent refinement, serving as the primary terminal for remote **Hermes** agents.

---

## 1. Hardware Resource Allocation

To ensure a fluid macOS experience without hitting the swap file, the system is designed to stay within a ~8.5GB VRAM footprint.

| Component | Model/Framework | Precision | Est. VRAM |
| --- | --- | --- | --- |
| **STT Engine** | Whisper Large V3 Turbo | FP16 (MLX) | ~1.6 GB |
| **Refiner LLM** | Qwen 2.5-3B-Instruct | 4-bit (MLX) | ~1.8 GB |
| **System/UI Overhead** | macOS + Voicebox (Tauri) | N/A | ~5.0 GB |
| **Total Footprint** |  |  | **~8.4 GB** |

---

## 2. Functional Pipeline

### Stage 1: Capture & Direct Transcription

* **Trigger:** Global hotkey activates the **Voicebox** (Tauri) recording overlay.
* **Audio Pipeline:** Raw PCM audio is streamed to the Python sidecar.
* **Inference:** `mlx-whisper` processes the buffer using the **Large V3 Turbo** model. It handles English and Portuguese natively.

### Stage 2: Context Parsing & Project Switching

* **Keyword Detection:** A lightweight string-parser scans the first 5 seconds of the transcript for the pattern: `Project [Name]`.
* **State Management:** If "Project Solyx" is detected, the system hot-swaps the active context buffer from `memory/projects/solyx.json`.
* **Fallback:** If no keyword is found, the system defaults to the `global_context.json`.

### Stage 3: Intent Reification (DSPy)

* **Input:** Raw Transcript + Active Project Context.
* **Logic:** A **DSPy Module** uses a **ChainOfThought** signature to:
  1. Clean transcript artifacts (stutters, phonetic errors).
  2. Align spoken intent with project-specific technical constraints.
  3. Generate a structured JSON payload for the execution agent.
* **Model:** The 4-bit **Qwen 2.5-3B** handles this refinement locally via `mlx-lm`.

### Stage 4: Remote Dispatch

* **Payload:** The refined JSON is dispatched via the **Hermes Webhook**.
* **UI Feedback:** Voicebox displays the "Optimized Intent" as solid text, providing visual confirmation that the mission has been sent to the remote host.

---

## 3. Project Structure

```text
voice-mission-control/
├── frontend/                     # Voicebox Tauri (React) UI
└── backend/                      # Python Sidecar (MLX/DSPy)
    ├── main.py                   # FastAPI entry point & Router
    ├── core/
    │   ├── stt_engine.py         # MLX-Whisper Turbo implementation
    │   └── refiner_engine.py     # MLX-LM local LLM wrapper
    ├── logic/
    │   ├── dspy_modules.py       # DSPy Signatures (IntentReification)
    │   └── context_manager.py    # Keyword parser & JSON loader
    ├── memory/
    │   ├── global_rules.json     # Fundamental persona & aesthetic rules
    │   └── projects/             # Project-specific context files
    │       ├── solyx.json
    │       ├── oopuo.json
    │       └── chimera.json
    └── dispatchers/
        └── hermes_client.py      # Remote agent communication logic
```

---

## 4. Intent Logic Signature (DSPy)

The **Refiner** follows a strict programmatic signature to ensure the output is machine-readable for the remote Hermes agent.

```python
class IntentReification(dspy.Signature):
    """
    Transform raw spoken audio transcripts into structured agent commands.
    Incorporate project-specific context and global design principles.
    """
    raw_transcript = dspy.InputField(desc="The unedited text from Whisper.")
    project_context = dspy.InputField(desc="Current project constraints and state.")
    global_rules = dspy.InputField(desc="Global UI and architectural guidelines.")

    structured_intent = dspy.OutputField(desc="JSON object containing 'action', 'parameters', and 'context_summary'.")
```

---

## 5. System Integrity & Latency Goals

* **STT Latency:** < 500ms for 10-second clips.
* **Refinement Latency:** < 1.2s using 4-bit quantization on M4 Neural Engine.
* **Context Isolation:** Project data is strictly siloed; the LLM only sees the active project file to prevent context drift and hallucinations across different ventures.
