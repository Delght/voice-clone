# Voice Cloning & AI Assistant

A fully self-hosted, privacy-first voice cloning and conversational AI system. No cloud APIs — all inference runs on your own hardware or your own server.

**Cloud-ready & hardware-agnostic** — develop locally on Apple Silicon, deploy anywhere (cloud VPS, home server, or any GPU machine) via Docker without changing a single line of code.

## What it does

- **Clone any voice** from a short audio reference (zero-shot)
- **Speak Vietnamese naturally** via a dedicated Vietnamese TTS model
- **Converse with an AI** — your voice in, AI's response out, in your cloned voice
- **Convert voice timbre** using RVC to match a target speaker model
- **Flexible Inputs:** Supports both uploading existing audio files and real-time microphone recording

## Tech Stack

| Layer | Technology |
| --- | --- |
| Speech-to-Text | [SYSTRAN/faster-whisper](https://github.com/SYSTRAN/faster-whisper) |
| Text-to-Speech | [fishaudio/fish-speech](https://github.com/fishaudio/fish-speech) + [pnnbao97/VieNeu-TTS](https://github.com/pnnbao97/VieNeu-TTS) |
| Voice Conversion | [IAHispano/Applio](https://github.com/IAHispano/Applio) (RVC) |
| LLM Brain | Qwen3.5 via [Mintplex-Labs/anything-llm](https://github.com/Mintplex-Labs/anything-llm) |
| Backend | [FastAPI](https://github.com/fastapi/fastapi) + [Pydantic](https://github.com/pydantic/pydantic) |
| Frontend | [Gradio](https://github.com/gradio-app/gradio) |
| Hardware | Apple MPS · Nvidia CUDA · CPU (auto-detected) |

## Architecture

```
User → Microphone
         ↓
   [STT: faster-whisper]  →  transcript
         ↓
   [LLM: Qwen3.5]         →  text response
         ↓
   [TTS: fish-speech]      →  audio
         ↓
   [RVC: Applio] (optional) → cloned voice
         ↓
      Speaker ← User
```

## Status

> Work in progress — building phase by phase.

- [x] Project structure & documentation
- [x] Phase 1: Audio processing & STT pipeline
- [x] Phase 2: TTS & voice cloning
- [ ] Phase 3: Microservices & API Gateway
- [ ] Phase 4: LLM integration
- [ ] Phase 5: UI
- [ ] Phase 6: Optimization & Docker

## Requirements

**Local Development (macOS Apple Silicon):**

- Apple Silicon Mac (M1/M2/M3/M4) with [Miniforge](https://github.com/conda-forge/miniforge)
- `brew install ffmpeg`

**Cloud / Server Deployment:**

- Docker + Docker Compose
- Nvidia GPU recommended (CUDA auto-detected), CPU fallback supported

## Getting Started

### Run scripts (local)

```bash
conda activate voice

# Phase 1 — STT + chunking
python scripts/chunk_audio.py --input data/raw/YOUR_FILE.mp3

# Phase 2 — fish-speech (English / multilingual TTS + cloning)
python scripts/tts_infer.py --text "This is an AI version of Thao. What do you want to talk about?" --ref data/chunks/speech_chunk_0001.wav --ref-text "America is a cutting edge economy, but our immigration system is stuck in the past."

# Phase 2 — VieNeu-TTS (Vietnamese TTS, optional cloning)
python scripts/vieneu_infer.py --text "Xin chào, hôm nay trời đẹp quá!"

# Phase 2 — Applio RVC (voice conversion; requires a .pth voice model)
python scripts/rvc_infer.py --input data/output.wav --model models/rvc/target_voice.pth
```

## Privacy

All inference runs locally. No data is sent to any external service.