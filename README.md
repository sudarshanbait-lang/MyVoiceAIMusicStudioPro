# My Voice AI Music Studio — Android + AI backend

## What this baseline actually does
- Android app records or uploads a voice sample, sends lyrics + music prompt to your server, receives and plays an MP3.
- Backend uses Coqui XTTS-v2 for **English spoken speech in a cloned voice**, MusicGen-small for short instrumental audio, and FFmpeg to mix them.
- **Not a singing synthesizer**. Stock XTTS-v2 does **not** officially support Hindi. Hindi lyrics deliberately return HTTP 422 instead of silently producing incorrect output. Hindi voice cloning/singing requires replacing the TTS engine with a compatible model.
- MusicGen-small has model-license restrictions; review its license before commercial use. Use only your own voice or one you are authorized to clone.

## Android APK via GitHub Actions (no Android Studio required)
1. Create a **new GitHub repository**.
2. Upload **contents of the `android/` folder** into the repository root, including hidden `.github/workflows/build-apk.yml`.
3. Open GitHub > Actions > Build Android APK > Run workflow.
4. Wait for green check; download `MyVoiceAI-debug-apk` from the workflow's Artifacts section.
5. Unzip artifact, install `app-debug.apk` on Android. This is a debug APK, not Play Store release.

## Backend on Ubuntu 22.04 / Python 3.10 (NVIDIA GPU recommended)
```bash
sudo apt update && sudo apt install -y ffmpeg python3.10 python3.10-venv
cd backend
python3.10 -m venv .venv
source .venv/bin/activate
pip install --upgrade pip setuptools wheel
pip install torch torchaudio --index-url https://download.pytorch.org/whl/cu121
pip install -r requirements.txt
uvicorn app:app --host 0.0.0.0 --port 8000
```
Coqui TTS dependencies may require platform-specific adjustments; Python 3.10 and a supported PyTorch/CUDA combination are recommended. Models download on first run and may require accepting model licenses. A CPU-only server may be extremely slow.

Test `http://SERVER_IP:8000/health` in your phone browser. In the Android app set `http://SERVER_IP:8000` and connect to the same Wi-Fi or an authorized private tunnel. Do not expose this demo server publicly: it has no authentication, quotas or TLS. For public deployment add HTTPS, authentication, consent checks, abuse limits and a background job queue.

## Limitations / next steps
- Hindi cloning: replace XTTS with a Hindi-capable, properly licensed model.
- Actual sung vocals: add singing voice synthesis/conversion; spoken TTS is not singing.
- Longer music: generate multiple sections and arrange them; current instrumental clip is looped.
- MusicGen may not consistently obey 'no vocals' instructions.
- Voice sample should be clean and ideally 15–30 seconds.
- This repository is source code. The APK is built using GitHub Actions, not precompiled here.
