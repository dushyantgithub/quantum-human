
# Quantum Human


This project creates a real-time health dashboard UI using Electron, Three.js, Whisper STT, Coqui TTS, and Raspberry Pi. It integrates Ultra Human Ring API data and enables voice interaction through local AI models.

---

## 🧠 Project Overview

**Platform**: Raspberry Pi 4B (8GB RAM)

**Technologies:**
- Electron.js (UI shell)
- Three.js (3D visuals)
- Python (STT & TTS logic)
- Whisper STT (16000Hz)
- Coqui TTS (32000Hz)
- OpenAI or local LLM API
- Ultra Human Ring API

---

## 🎯 Features

- 🫀 3D beating heart using Three.js
- 💓 Live BPM sync from Ultra Human API
- 📊 Real-time graphs for heart rate, sleep, movement
- 🎙️ Whisper STT via mic trigger
- 🌈 Audio visualizer during recording
- 🤖 AI responses from OpenAI/local LLM
- 🗣️ Coqui TTS for voice replies
- 📂 Analyze local files via LLM
- 🔁 Offline capable + auto-boot on Pi

---

## 🛠️ Setup Instructions

### 1. Raspberry Pi OS Prep

```bash
sudo apt update && sudo apt upgrade
sudo apt install git python3 python3-pip ffmpeg libasound2-dev portaudio19-dev
```

### 2. Node.js + Electron Setup

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install -y nodejs
```

```bash
mkdir ultra-human-ui && cd ultra-human-ui
npm init -y
npm install electron three
```

### 3. UI Layout (Three.js)

- Load a 3D heart model (`.gltf/.fbx`)
- Animate heartbeat using BPM from API
- Surround with graph containers + mic icon

### 4. Whisper STT (16000Hz)

```bash
pip install openai-whisper
```

```bash
arecord -f S16_LE -r 16000 -d 5 input.wav
```

```python
import whisper
model = whisper.load_model("base")
result = model.transcribe("input.wav")
print(result["text"])
```

### 5. OpenAI / Local LLM

```python
import requests
payload = {"prompt": result["text"]}
response = requests.post("http://localhost:11434/api", json=payload)
reply = response.json()["response"]
```

### 6. Coqui TTS (32000Hz)

```bash
pip install TTS
```

```python
from TTS.api import TTS
tts = TTS(model_name="tts_models/en/ljspeech/tacotron2-DDC")
tts.tts_to_file(text=reply, file_path="output.wav")
```

```bash
aplay -r 32000 output.wav
```

### 7. Audio Visualizer

- Use Web Audio API
- Optionally render mic waveform or FFT bars in canvas

### 8. Ultra Human API

- Fetch data every 15 minutes (via setInterval or `cron`)
- Use BPM to control heart animation
- Display sleep, heart rate, and movement data

### 9. Local File Access

- Use Electron file picker
- Support: `.csv`, `.txt`, `.json`, `.pdf`
- Send content to LLM API for analysis

### 10. Optimizations for Raspberry Pi

- Reduce mesh complexity in 3D heart
- Cap FPS between 40–60
- Enable GPU flags in Electron

### 11. Auto Start on Boot

Create a systemd service:

```ini
[Unit]
Description=Ultra Human UI
After=network.target

[Service]
ExecStart=/usr/bin/npm start
WorkingDirectory=/home/pi/ultra-human-ui
StandardOutput=inherit
StandardError=inherit
Restart=always
User=pi

[Install]
WantedBy=default.target
```

---

## 🧰 Folder Structure

```
ultra-human-ui/
├── main.js
├── index.html
├── renderer.js
├── assets/
│   ├── models/
│   └── audio/
├── python/
│   ├── whisper_stt.py
│   ├── coqui_tts.py
│   └── llm_query.py
└── package.json
```

---

## 📦 Dependencies

- Electron
- Three.js
- `openai-whisper`
- `TTS` (Coqui)
- Python 3.12+
- Node.js 18+

---

## 🚀 Future Enhancements

- Voice command classification
- Conversation history cache
- Skinnable heart/graph themes
- OTA update mechanism

---

## 📌 Notes

- Everything can run fully offline
- Modular design for UI, STT, TTS, LLM
- Whisper.cpp + TTS server mode are optional for performance

---

Let’s get building! 🧪
