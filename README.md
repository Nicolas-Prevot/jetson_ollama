# Jetson Orin LLM Setup: Ollama + Open WebUI + Tailscale

This repository provides a reproducible setup for running Ollama (local LLMs with GPU acceleration) and Open WebUI (web UI and API) on NVIDIA Jetson Orin via Docker. Access remotely via Tailscale.

## Prerequisites
- NVIDIA Jetson Orin 64GB with JetPack 6.x+ (Ubuntu 22.04 ARM64).
- Docker installed with NVIDIA GPU support (see NVIDIA docs or previous guides).
- NVMe SSD mounted at `/mnt/ssd` (adjust paths if different).
- Tailscale installed and authenticated for remote access (install: `curl -fsSL https://tailscale.com/install.sh | sh`, then `sudo tailscale up`).
- Git cloned this repo: `git clone <your-repo-url> && cd jetson-llm-setup`.
- Create directories for persistence (prevents log errors):
  ```bash
  sudo mkdir -p /mnt/ssd/ollama/logs /mnt/ssd/ollama/models/ollama/models /mnt/ssd/open-webui
  sudo chown -R $USER:$USER /mnt/ssd/ollama /mnt/ssd/open-webui
  ```

## Setup Instructions
1. **Start Services:**
   ```bash
   docker compose up -d
   ```

2. **Verify:**
    - Ollama: `curl http://localhost:11434` (should say "Ollama is running").
    - Open WebUI: Browse to http://localhost:3000 (sign up on first access).
    - GPU: `docker logs ollama` (look for CUDA/GPU detection).
    - Tailscale IP: `tailscale ip -4` – use `http://<ip>:3000` remotely.

3. **Pull a Model:**
   ```bash
   docker exec -it ollama ollama pull llama3.1:8b  # Or via WebUI
   ```

4. **Remote Access:**
    - From other devices on Tailscale: `http://<jetson-tailscale-ip>:3000` for UI.
    - API: `http://<jetson-tailscale-ip>:11434/api/*` (Ollama) or `http://<jetson-tailscale-ip>:3000/api/*` (WebUI).

## Usage
- Web UI: Chat with models, manage downloads.
- API Calls: e.g., `curl http://<ip>:11434/api/generate -d '{"model": "llama3.1", "prompt": "Hello"}'`.
- **Stop:** `docker compose down`.
- **Update:** Edit yaml, then `docker compose up -d --pull always`.

## Troubleshooting
- GPU Issues: Check `docker logs ollama` for errors; ensure NVIDIA container toolkit is configured.
- Ports: If firewalled, `sudo ufw allow 11434 3000`.
- Logs: `docker compose logs -f`.

## License
MIT – Feel free to modify!