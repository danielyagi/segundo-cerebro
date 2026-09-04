# Alfred (VPS bc6r) — Sub-Agents com Ollama Local

## Visão Geral

Container `alfred` no VPS `179.198.101.163` (bc6r, srv1862885.hstgr.cloud) executa Hermes v0.20.0 via fork Hostinger (`ghcr.io/hostinger/hvps-hermes-agent:latest`). Utiliza **Ollama local** como provedor de LLM para orquestrador principal + sub-agents.

## Arquitetura

### Docker Compose (`/docker/hermes-agent-bc6r/docker-compose.yml`)

```yaml
services:
  hermes-agent:
    image: ghcr.io/hostinger/hvps-hermes-agent:latest
    env_file:
      - .env
    restart: unless-stopped
    ports:
      - 4860
    volumes:
      - ./data:/opt/data
    networks:
      - ollama-1in7_default      # rede externa do Ollama
      - browserless-qxtx_default # rede externa do Browserless
      - chrome_default            # rede externa do Chrome
```

### Variáveis de Ambiente (`.env`)

| Variável | Valor | Função |
|---|---|---|
| `LLM_PROVIDER` | `ollama` | Provider global |
| `OLLAMA_BASE_URL` | `http://ollama:11434/v1` | Endpoint OpenAI-compatível do Ollama |
| `MODEL_NAME` | `hermes3:8b` | Modelo orquestrador principal |
| `CODE_MODEL_NAME` | `qwen2.5-coder:7b` | Modelo sub-agent de código |
| `VISION_MODEL_NAME` | `qwen2.5vl:7b` | Modelo sub-agent de visão |
| `ADMIN_USERNAME` | `hermes` | Admin dashboard |
| `ADMIN_PASSWORD` | (gerada) | Admin password |
| `TRAEFIK_HOST` | `srv1862885.hstgr.cloud` | Host Traefik |

### Modelos no Ollama (container `ollama-1in7-ollama-1`)

| Modelo | ID | Tamanho |
|---|---|---|
| `hermes3:8b` | 4f6b83f30b62 | 4.7 GB |
| `qwen2.5-coder:7b` | dae161e27b0e | 4.7 GB |
| `qwen2.5vl:7b` | 5ced39dfa4ba | 6.0 GB |

Ollama expõe API OpenAI-compatível em `http://ollama:11434/v1` (docker network). Porta externa: `0.0.0.0:32783`.

### Perfis no Hermes

Dentro do container `alfred`, os profiles disponíveis em `/opt/data/profiles/`:
- `coder` (perfil legado, usa `anthropic/claude-opus-4.8-fast` via openrouter)
- `apostador` (outro perfil)

**Não há perfil `alfred` dedicado** — o container usa env vars como defaults.

## Sub-Agents e Modelos

### Mapeamento Atual

O Hermes (fork Hostinger) reconhece as env vars `CODE_MODEL_NAME` e `VISION_MODEL_NAME` para sub-agents especializados:

1. **Orquestrador principal** → `hermes3:8b` (via `MODEL_NAME`)
2. **Sub-agent coder** → `qwen2.5-coder:7b` (via `CODE_MODEL_NAME`)
3. **Sub-agent visão** → `qwen2.5vl:7b` (via `VISION_MODEL_NAME`)
4. **3º sub-agent** → **não configurado** (sem env var para um 4º modelo)

### Problema: Sub-Agent "Restante"

Foi solicitado um 3º sub-agent com a LLM restante, mas só existem 3 modelos no Ollama, todos já alocados. Soluções possíveis:

1. **Pull `qwen2.5:7b`** (modelo base texto, ~4.7 GB) — mais leve, adequado para tarefas genéricas
2. **Reutilizar `hermes3:8b`** — orquestrador e sub-agent seriam o mesmo (não ideal)
3. **Usar `qwen2.5-coder:7b` para 2 sub-agents** — coder e genérico (não ideal)

## Acesso SSH

```python
import sys; sys.path.insert(0,'/tmp/psite')
import paramiko
s = paramiko.SSHClient()
s.set_missing_host_key_policy(paramiko.AutoAddPolicy())
s.connect('179.198.101.163', username='root', password='VitoYagi#1234', timeout=20)
# comandos via s.exec_command()
s.close()
```

**Nota:** Paramiko instalado em `/tmp/psite`. Para comandos no container: `docker exec alfred <comando>`.

## Performance do Ollama

Logs mostram prompt processing de ~50 tok/s e eval de ~8.4 tok/s para `hermes3:8b` com context de 2050 tokens — execução em CPU (sem GPU).

## Referências

- Repositório imagem: `ghcr.io/hostinger/hvps-hermes-agent:latest`
- Fonte: `github.com/hostinger/docker-compose-catalog` (commit `65a093f`)
- Hermes v0.20.0 (fork Hostinger, não upstream NousResearch)