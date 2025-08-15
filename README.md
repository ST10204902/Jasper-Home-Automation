# Jasper-Home-Automation
---

# **Home LLM + Home Assistant Integration Spec Sheet**

## **Core Goals**

* **Use cases:**

  * Voice/text queries
  * Smart-home control via Home Assistant API
  * Google Calendar events creation
  * Reminders, timers, web searches
* **Performance priority:** Low latency, “snappy” replies
* **Offline capability:** Must operate locally with optional internet for some features
* **Privacy:** 100% free/open-source stack, no external cloud calls. Remote access via self-hosted DNS/VPN.

---

## **Hardware**

* **AI Host**: Old gaming laptop (GTX 1050 4GB, Windows)

  * Can also be used for personal tasks outside AI hosting
* **Home Assistant Hub**: Old office desktop (switch to Linux for efficiency)
* **Both devices**: LAN-connected, capable of running 24/7
* **Remote access requirement**: Tailscale or ZeroTier (for global access)

---

## **Software Stack**

### **1. LLM Backend (Laptop)**

* **Model host**:

  * [Ollama](https://ollama.com/) or [llama.cpp](https://github.com/ggerganov/llama.cpp)
  * Quantized 7B–8B model for speed on GTX 1050
* **RAG for context**:

  * ChromaDB or SQLite-based embeddings store
  * Small bge or MiniLM embeddings
* **Web interface**:

  * [Open WebUI](https://github.com/open-webui/open-webui) for chat, admin, and history
* **API compatibility**:

  * Expose OpenAI-compatible API endpoint for HA/other clients
* **Offline search**:

  * Local doc ingestion for memory-like features

---

### **2. Smart-Home Integration (Desktop)**

* **OS**: Debian/Ubuntu minimal for lower resource use
* **Home Assistant Core** with Assist disabled (LLM will handle queries)
* **LLM → HA connection**:

  * Home Assistant REST API or WebSocket API for controlling entities
* **Future expansion**: Lights, plugs, climate, cameras

---

### **3. Speech (Future Phase)**

* **STT**: Start with Samsung STT → send text to LLM; later add [Whisper.cpp](https://github.com/ggerganov/whisper.cpp) for offline speech recognition
* **Wake word**: [OpenWakeWord](https://github.com/dscripka/openwakeword) for “Hey Jarvis” or custom phrase
* **TTS**: [Piper](https://github.com/rhasspy/piper) for low-latency local speech output

---

## **Networking**

* **LAN Access**: Host LLM API + WebUI on local IP, reverse proxy with Caddy/Nginx
* **Remote Access**:

  * Tailscale/ZeroTier for secure mesh networking
  * Custom DNS to route `assistant.myhome.net` to home server IP

---

## **Privacy & Filtering**

* **Minimal filtering**: Self-hosted model without hardcoded moderation layers
* **All APIs local**: Any external lookups routed through your home network’s resolver

---

## **Operational Notes**

* **Performance tuning**:

  * Prioritize quantized models for speed
  * GPU acceleration via CUDA build of llama.cpp/Ollama
* **Context strategy**:

  * Small base context, inject only relevant RAG snippets
  * Avoid huge prompts to keep latency low
* **Offline failover**:

  * If internet drops, disable calendar/web search features gracefully

---

## **Initial Deployment Plan**

1. Install Ollama or llama.cpp on laptop with CUDA
2. Test quantized 7B model for <2s latency
3. Install Open WebUI for chat access
4. Set up Home Assistant on desktop (Linux)
5. Connect LLM to HA API
6. Add Tailscale for remote access
7. Integrate Google Calendar API (via local proxy)
8. Add RAG for reminders and persistent facts
9. Later: implement STT + wake word + TTS

---

