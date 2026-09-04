# Hermes Containers: Jarvis (i3a1) e Alfred (bc6r)

> Atualizado em: 04/09/2026

## Visão Geral

Dois containers Hermes Agent rodando em VPS Hostinger:

| Container | Apelido | VPS | IP | Porta Gateway | Porta Dashboard | Bind Mount |
|-----------|---------|-----|----|---------------|-----------------|------------|
| `jarvis` (ex-`hermes-agent-i3a1-hermes-agent-1`) | Jarvis | i3a1 (srv1668986) | `76.13.29.94` | `32784` | `4860` | `/docker/hermes-agent-i3a1/data` → `/opt/data` |
| `alfred` (ex-`hermes-agent-bc6r-hermes-agent-1`) | Alfred | bc6r (srv1862885) | `179.198.101.163` | `32785` | `4860` | `/docker/hermes-agent-bc6r/data` → `/opt/data` |

## Credenciais SSH

- **Usuário:** root
- **Senha:** `VitoYagi#1234`
- **Autenticação:** password (configurado em `~/.ssh/config` como host `vps2` para `179.198.101.163`)
- **Nota:** `sshpass` não disponível no container Hermes. Usar `paramiko` (Python, instalado em `/tmp/psite`) para automação SSH.

## Versão Hermes

- **Imagem:** `ghcr.io/hostinger/hvps-hermes-agent:latest` (fork Hostinger, não upstream NousResearch)
- **Versão atual:** v0.20.0 (2026.8.3)
- **Update:** `hermes update` não funciona dentro do container Docker. Para atualizar:
  1. `docker pull ghcr.io/hostinger/hvps-hermes-agent:latest` (pull via SSH)
  2. `docker restart <container-name>`
- **Bloqueio:** `apt-get install` sem permissão de write (`/var/lib/apt/lock`) — mas VPS root funciona via SSH direto.

## Skills: Transferência entre Containers

### 03/09/2026 — Transferência i3a1 → bc6r

Skills do perfil `coder` foram copiadas de Jarvis (i3a1) para Alfred (bc6r):

| Métrica | Source (Jarvis) | Dest (Alfred) | Match |
|---------|----------------|---------------|-------|
| skills/ SKILL.md | 174 | 185 | ✅ (185 inclui dirs built-in do container) |
| profiles/*/skills SKILL.md | 188 (96+98) | 188 | ✅ |
| Total SKILL.md | ~362 | ~362 | ✅ |
| composio/.agents skills | 18 | 18 | ✅ |
| MD5 sample | `51c73a9d` | `51c73a9d` | ✅ idêntico |

**O que foi transferido (7.6MB):**
- Skills do perfil `coder` (96 SKILL.md)
- Skills globais (174 SKILL.md)
- Skills do composio (18 SKILL.md)

**O que NÃO foi transferido:**
- Repositório `composio` SDK (python/ts/docs/.git = 400MB)
- `profiles/coder/hermes_agente` (1.3GB deploy runtime)

## Comandos Úteis

### SSH via paramiko (dentro do container)
```python
import sys; sys.path.insert(0,'/tmp/psite')
import paramiko
t = paramiko.SSHClient()
t.set_missing_host_key_policy(paramiko.AutoAddPolicy())
t.connect('179.198.101.163', username='root', password='VitoYagi#1234')
# comandos...
t.close()
```

### Renomear container (zero-downtime)
```bash
docker rename <old-name> <new-name>
```

|### Pull + Restart (update)
```bash
docker pull ghcr.io/hostinger/hvps-hermes-agent:latest
docker restart <container-name>
```

### Verificar versão
```bash
docker exec <container-name> hermes --version
```

## Configuração de Sub-Agents via Env Vars (04/09/2026)

O container `alfred` (bc6r) utiliza env vars do `.env` para definir provedor e modelos:

| Env Var | Valor | Função |
|---------|-------|--------|
| `LLM_PROVIDER` | `ollama` | Provider global |
| `OLLAMA_BASE_URL` | `http://ollama:11434/v1` | Endpoint OpenAI-compatível do Ollama |
| `MODEL_NAME` | `hermes3:8b` | Orquestrador principal |
| `CODE_MODEL_NAME` | `qwen2.5-coder:7b` | Sub-agent de código |
| `VISION_MODEL_NAME` | `qwen2.5vl:7b` | Sub-agent de visão |

**Modelos no Ollama (container `ollama-1in7-ollama-1`):**

| Modelo | ID | Tamanho |
|--------|----|---------|
| `hermes3:8b` | 4f6b83f30b62 | 4.7 GB |
| `qwen2.5-coder:7b` | dae161e27b0e | 4.7 GB |
| `qwen2.5vl:7b` | 5ced39dfa4ba | 6.0 GB |

**Nota:** Apenas 3 modelos carregados. Não há 4º modelo para um 3º sub-agent "restante". Opção: fazer pull de `qwen2.5:7b` (base, texto puro, ~4.7 GB).

### Redes Docker

O container `alfred` está conectado a 3 redes externas:
- `ollama-1in7_default` — acesso ao Ollama em `http://ollama:11434`
- `browserless-qxtx_default` — acesso ao Browserless
- `chrome_default` — acesso ao Chrome

### Arquivos de Configuração

- Compose: `/docker/hermes-agent-bc6r/docker-compose.yml` (fora do bind mount)
- Env: `/docker/hermes-agent-bc6r/.env`
- Dados: `/docker/hermes-agent-bc6r/data/` → montado em `/opt/data`