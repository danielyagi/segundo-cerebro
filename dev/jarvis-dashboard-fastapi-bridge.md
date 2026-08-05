# JARVIS Dashboard - FastAPI Bridge

**Data:** 05/08/2026
**Categoria:** Desenvolvimento / Voice AI

## Resumo

Construção de um Dashboard no estilo JARVIS (Homem de Ferro) como bridge FastAPI para o Hermes Agent. Diferente do JARVIS AI anterior (eadmin2/jarvis_ai), este é um frontend web completo com SSE streaming, STT (faster-whisper), TTS (edge-tts), e painel de controle estilo HUD.

**Repositório:** `/opt/data/Projects/JARVIS/`

## Stack

| Componente | Tecnologia | Detalhes |
|---|---|---|
| Backend | FastAPI + uvicorn | Porta 8787 |
| Frontend | HTML + CSS + JS puro | HUD Ironman estético |
| Streaming | SSE (Server-Sent Events) | Deltas em tempo real |
| STT | faster-whisper (base, pt) | Via Web Audio API + VAD 550ms |
| TTS | edge-tts (pt-BR-AntonioNeural) | + speechSynthesis fallback |
| API | Hermes Gateway `/v1/runs` | Porta 8642 |

## Estrutura de Arquivos

```
/opt/data/Projects/JARVIS/
├── server.py              # FastAPI bridge (312 lines)
├── static/
│   ├── index.html         # HUD UI (93 lines)
│   ├── app.js             # Frontend logic (571 lines)
│   └── styles.css         # HUD styling (463 lines)
├── scripts/
│   ├── start.sh           # Server launcher
│   ├── register_gateway.sh # Gateway registration
│   └── jarvis_mobile.sh   # Mobile access (Termux)
├── tests/
│   └── test_kill_switch.js # Kill switch regression (14/14 ✅)
├── .env                   # Config (API key redacted)
├── .venv/                 # Python 3.12.13 venv
└── README.md              # Documentação completa
```

## Endpoints da Bridge

| Rota | Método | Função |
|---|---|---|
| `/` | GET | Serve HUD HTML |
| `/api/status` | GET | Status da bridge + gateway |
| `/api/run` | POST | Envia comando ao Hermes |
| `/api/runs/{run_id}` | GET | Status de uma execução |
| `/api/runs/{run_id}/events` | GET | SSE streaming de deltas |
| `/api/approval` | POST | Decisão de aprovação |
| `/api/stt` | POST | Transcrição de áudio |
| `/api/tts` | POST | Text-to-Speech |

## Descobertas sobre Hermes API

- Base path: `/v1/runs` (não `/api/v1/runs`)
- Campo de envio: `input` (não `message`)
- Health check: `/health` (não `/api/status`)
- Aprovação: valores `once|session|always|deny` (não `allow`)
- SSE events: `data: {"event":"message.delta","delta":"..."}` (formato NDJSON)
- Event names: `message.delta`, `run.complete`, `run_complete` (todos aceitos)

## Pipeline de Funcionamento

1. Áudio do microfone → Web Audio API (VAD 550ms silence) → MediaRecorder stop
2. Áudio → POST `/api/stt` → faster-whisper (base, pt) → texto
3. Texto → POST `/api/run` → Hermes Agent → SSE streaming de deltas
4. Deltas → edge-tts (pt-BR-AntonioNeural) → MP3 chunks → Audio.play()
5. Fallback TTS: speechSynthesis (navegador)

## Kill Switch

- Botão de parada de emergência no HUD
- Para áudio + limpa fila + cancela speechSynthesis + seta killVoice=true
- Testes: 14/14 passando

## Acesso Externo

**Problema:** Porta 8787 escutando em `0.0.0.0` dentro do container Docker, mas IP público `76.13.29.94` não roteia para dentro do container.

**Solução:** Túnel SSH apontando para IP do container (`172.16.1.2`), não para `localhost`:
```bash
ssh -L 8787:172.16.1.2:8787 root@76.13.29.94 -N
```

**Nota:** Container IP confirmado via `/etc/hosts`: `172.16.1.2`

## Portas

| Porta | Serviço | Bind |
|---|---|---|
| 8787 | JARVIS Dashboard (FastAPI) | 0.0.0.0 ✅ |
| 8642 | Hermes Gateway API | 127.0.0.1 (precisa 0.0.0.0) |
| 4860 | Hermes Dashboard | 0.0.0.0 ✅ |

## Troubleshooting

### SSH tunnel aponta para localhost do host, não container
- **Sintoma:** `Connection refused` ao acessar `http://localhost:8787` no celular após tunnel SSH
- **Causa:** `ssh -L 8787:localhost:8787` encaminha para porta do HOST, mas JARVIS roda no CONTAINER
- **Solução:** Usar IP do container: `ssh -L 8787:172.16.1.2:8787`

### API key rejeitada
- **Sintoma:** `401 Unauthorized` ao chamar `/v1/runs`
- **Causa:** Gateway rodando com .env desatualizado (precisa restart completo, não apenas `gateway restart`)
- **Solução:** Verificar se .env foi modificado após último boot do container

### Gateway restart não aplica mudanças
- **Sintoma:** PID 1 (entrypoint) continua com configuração antiga
- **Causa:** `hermes gateway restart` não reinicia o processo supervisor (PID 1)
- **Solução:** `kill -9 <PID>` e reiniciar manualmente, ou reboot do container

### Porta 8787 acessível internamente mas não externamente
- **Causa 1:** NAT loopback — IP público não roteia de dentro da VPS
- **Causa 2:** Firewall do provedor (Hostinger) não está realmente aplicando as regras
- **Teste:** curl de fora da VPS (via 4G/outra máquina)