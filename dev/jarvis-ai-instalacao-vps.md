# JARVIS AI - Instalação e Configuração na VPS Hostinger

**Data:** 04/08/2026
**Categoria:** Desenvolvimento / Voice AI

## Resumo

Instalação completa do [JARVIS AI](https://github.com/eadmin2/jarvis_ai) (eadmin2/jarvis_ai) — pipeline de voz com TTS local (Piper), STT local (faster-whisper), integrado ao Hermes Agent via API. Tudo rodando em VPS Hostinger dentro do container Docker do Hermes.

## Stack

| Componente | Tecnologia | Detalhes |
|---|---|---|
| LLM | Hermes Agent API (gateway) | `http://127.0.0.1:8642` |
| TTS | Piper TTS (local) | Modelo `pt_BR-faber-medium` (63MB) |
| STT | faster-whisper (local) | Modelo `Systran/faster-whisper-base` |
| Server | FastAPI + WebSocket | Porta 8765 |
| HUD | HTML/JS | Porta 443 (HTTPS) + 8766 (HTTP) |
| Dashboard Proxy | Proxy reverso | Porta 9443 → Hermes Dashboard 4860 |

## Estrutura de Arquivos

```
/opt/data/jarvis_ai/
├── server/
│   ├── server.py          # FastAPI WebSocket server (patched)
│   ├── tts_server.py      # Flask wrapper para Piper TTS (created)
│   ├── config/server.yaml # Config (engine: custom, piper)
│   ├── Dockerfile         # Container image definition
│   └── .venv/             # Virtualenv com deps
├── worker/
│   └── worker_stats.py    # Patched (missing import os)
/opt/data/tts_engine/piper/
├── piper/                 # Piper TTS binary
├── model.onnx             # pt_BR-faber-medium ONNX model
└── model.onnx.json        # Model config JSON
/opt/data/.hermes/
├── .env                   # API_SERVER_KEY, JARVIS_HUD_TOKEN, ELEVENLABS_API_KEY
└── plugins/hud_display/   # HUD display plugin for Hermes
```

## Patches Realizados

### 1. server.py — Suporte a TTS custom (Piper)
- Função `tts_chunks_sync` modificada para detectar `VOICE_ENGINE=custom`
- Faz POST para `TTS_ENDPOINT` em vez de `api.elevenlabs.io`
- Usa `pt_BR-faber-medium` (voz masculina, qualidade média)

### 2. server.yaml — Adaptação Linux
- `engine: custom` em vez de `elevenlabs`
- `model: piper_faber_medium_pt_BR`
- Portas ajustadas: dashboard proxy → `http://127.0.0.1:4860`
- WebSocket em `0.0.0.0:8765`

### 3. worker_stats.py — Bug fix
- Adicionado `import os` faltando (usava `os.environ` sem import)

### 4. tts_server.py — Novo arquivo
- Flask wrapper para binário Piper
- POST `/tts` recebe texto, retorna WAV audio
- Converte WAV com header → PCM raw para consumo do server.py WebSocket

### 5. Dockerfile + docker-compose — Containerização
- `Dockerfile` baseado em `hermes-agent:latest` (Python 3.12)
- Serviço `jarvis` no `docker-compose.yml` com `network_mode: host`
- Monta `/opt/data` para compartilhar `.env`, certs, Piper TTS

## Portas

| Porta | Serviço | Status |
|---|---|---|
| 8642 | Hermes Gateway | UP |
| 4860 | Hermes Dashboard | UP |
| 8765 | JARVIS WebSocket | UP |
| 443 | JARVIS HTTPS (HUD) | UP |
| 8766 | JARVIS HTTP (HUD) | UP |
| 9443 | Dashboard Proxy | UP |
| 5000 | Piper TTS | UP |
| 32772 | External (Hostinger proxy) | Configurar |

## Acesso

**Dentro do container (funciona):**
```bash
https://127.0.0.1:443/hud/           # HUD web
http://127.0.0.1:8765/               # WebSocket
http://127.0.0.1:8765/hud/           # HUD via HTTP
```

**Token de acesso:** `jarvis-6a28dc` (via header `X-Jarvis-Token`)

**De outro container Docker na mesma VPS:**
- Descobrir IP do container JARVIS: `docker inspect <container-name> | grep IPAddress`
- Acessar via IP interno Docker (ex: `172.17.0.X`)

**Fora da VPS (pendente):**
- Configurar mapeamento de portas no painel Hostinger
- 32772 → 8765 (WebSocket) ou 32772 → 443 (HTTPS HUD)

## Pipeline de Funcionamento

1. Áudio do microfone → WebSocket → STT (faster-whisper)
2. Texto transcrito → Hermes Agent API → resposta LLM
3. Resposta LLM → Piper TTS → áudio TTS → WebSocket
4. HUD web mostra status e interações em tempo real

## Troubleshooting

### Gateway Hermes não responde
- `kill -9 <PID>` do gateway e reiniciar
- Verificar se plugin hud_display não está causando erro de carregamento
- Gateway precisa ser iniciado DENTRO do container

### Porta 8642 não aparece no LISTEN
- Gateway pode estar travado em estado S (sleeping) sem bind
- Solução: matar processo e reiniciar

### Certificados TLS (make-certs.sh)
- Script original é macOS-only (usa `ipconfig getifaddr en0`)
- Para Linux: usar `hostname -I | awk '{print $1}'` ou IP fixo

### Piper TTS não carrega
- Verificar `LD_LIBRARY_PATH` apontando para diretório do binário Piper
- Verificar caminho do modelo ONNX + JSON