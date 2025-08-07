Quantum Human
This documentation outlines how to build a real-time health dashboard UI using Electron, Three.js, Whisper STT, Coqui TTS, and Raspberry Pi. The system interfaces with Ultra Human Ring API to display health data and enables voice interaction through local AI models.

Project Overview
Platform: Raspberry Pi 4B (8GB RAM)
Technologies Used:
	•	Electron.js (UI shell)
	•	Three.js (3D visuals)
	•	Python (for STT & TTS)
	•	Whisper STT (16000Hz audio)
	•	Coqui TTS (32000Hz audio)
	•	OpenAI API or Local LLM
	•	Ultra Human Ring API

Features
	•	3D beating heart at center using Three.js
	•	Heartbeat animation synced to BPM data from Ultra Human API
	•	Surrounding live graphs for heart rate, movement, sleep, etc.
	•	Mic icon to trigger STT (Whisper)
	•	Audio visualizer animation during recording
	•	STT text sent to AI (OpenAI/local)
	•	Response converted to speech using Coqui TTS
	•	Ability to access and analyze local files via LLM
	•	Fully local processing possible
	•	Starts automatically on Raspberry Pi boot

Setup Steps
1. Raspberry Pi OS Preparation
	•	Use Raspberry Pi OS 64-bit (Lite or Full)
	•	Install dependencies:
	•	sudo apt update && sudo apt upgrade sudo apt install git python3 python3-pip ffmpeg libasound2-dev portaudio19-dev
2. Node.js + Electron Environment
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash - sudo apt install -y nodejs
	•	Create project directory:
	•	mkdir ultra-human-ui && cd ultra-human-ui npm init -y npm install electron three
3. UI Layout (Electron + Three.js)
	•	Load a main.js file to create the Electron window
	•	Load an index.html that includes:
	•	Three.js canvas with a 3D heart (GLTF/FBX)
	•	Graph containers for real-time health metrics
	•	Mic icon that triggers recording
	•	Use BPM value from API to animate the heartbeat in real time
4. Whisper STT Integration (16000Hz)
	•	Install Whisper:
	•	pip install openai-whisper
	•	Record audio on mic click (using arecord or PyAudio):
	•	arecord -f S16_LE -r 16000 -d 5 input.wav
	•	Transcribe:
	•	import whisper model = whisper.load_model("base") result = model.transcribe("input.wav") print(result["text"])
5. OpenAI / Local LLM API
	•	Send result["text"] to OpenAI or local server:
	•	import requests payload = {"prompt": result["text"]} response = requests.post("http://localhost:11434/api", json=payload) reply = response.json()["response"]
6. Coqui TTS (32000Hz)
	•	Install Coqui TTS:
	•	pip install TTS
	•	Run TTS locally:
	•	from TTS.api import TTS tts = TTS(model_name="tts_models/en/ljspeech/tacotron2-DDC") tts.tts_to_file(text=reply, file_path="output.wav")
	•	Play with:
	•	aplay -r 32000 output.wav
7. Audio Visualizer
	•	Use Web Audio API in JS or visualize using mic waveform during recording
	•	Optional: draw FFT-based wave patterns using canvas overlay
8. Ultra Human API Integration
	•	Use API key to fetch data from Ultra Human Ring
	•	Poll data every 15 minutes using a scheduled task (setInterval or cron job)
	•	Extract BPM to control heart animation in Three.js
	•	Poll heart rate, movement, sleep, etc. and send to UI for graph updates
9. Accessing Local Files
	•	Allow file system access via Electron/Node.js
	•	Allow users to select files (CSV, TXT, PDF, JSON)
	•	Parse and send file contents to local LLM API for analysis
10. Optimizing for Raspberry Pi
	•	Reduce Three.js mesh complexity
	•	Limit refresh rate to 30–60 FPS
	•	Use GPU acceleration settings in Electron
11. Auto Start on Boot
	•	Use ~/.bashrc, pm2, or a systemd service to start the Electron app on boot
	•	Example using systemd:
	•	[Unit] Description=Ultra Human UI After=network.target  [Service] ExecStart=/usr/bin/npm start WorkingDirectory=/home/pi/ultra-human-ui StandardOutput=inherit StandardError=inherit Restart=always User=pi  [Install] WantedBy=default.target

Future Enhancements
	•	Add voice command classification (intent detection)
	•	Cache history on disk (JSON)
	•	Switchable avatars or heart themes
	•	OTA update mechanism

Folder Structure (Suggested)
ultra-human-ui/ ├── main.js ├── index.html ├── renderer.js ├── assets/ │   ├── models/ │   └── audio/ ├── python/ │   ├── whisper_stt.py │   ├── coqui_tts.py │   └── llm_query.py └── package.json

Dependencies Summary
	•	Electron (UI wrapper)
	•	Three.js (3D rendering)
	•	Whisper (STT via openai-whisper)
	•	Coqui TTS (via TTS)
	•	Node.js (API bridge + UI logic)
	•	Python (voice processing)

Final Notes
	•	All components can run offline on Raspberry Pi
	•	You can modularize each part (UI, STT, TTS, LLM)
	•	Audio processing can be optimized further using whisper.cpp or TTS server mode
