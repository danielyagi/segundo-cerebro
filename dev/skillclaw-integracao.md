# SkillClaw - Instalação e Integração com Hermes

## Data: 30/07/2026

## O que é

SkillClaw é um proxy local que orquestra skills, gerencia modelos LLM e tem integração nativa com o Hermes Agent. Atua como um roteador de chamadas LLM, permitindo escalabilidade e evolução de skills.

## Instalação

```bash
git clone https://github.com/AMAP-ML/SkillClaw.git /opt/data/skillclaw_install/SkillClaw
cd /opt/data/skillclaw_install/SkillClaw
bash scripts/install_skillclaw.sh
```

O script de instalação cria um venv em `.venv` e instala as dependências (fastapi, uvicorn, httpx, openai, etc.).

## Configuração

O setup interativo (`skillclaw setup`) apresenta um wizard que pergunta:
- CLI Agent (escolher `hermes`)
- LLM provider (pode ser `custom` ou `openrouter`)
- API base URL e API key
- Skills directory (padrão: `~/.skillclaw/skills`)
- PRM (Quality Scoring) - desabilitar para first run
- Proxy port (padrão: 30000)

### Configuração manual via YAML

A configuração fica em `~/.skillclaw/config.yaml`. Para integrar com Hermes:

```yaml
claw_type: hermes
llm:
  api_base: ''
  api_key: ''
  api_mode: chat
  model_id: ''
  provider: custom
prm:
  enabled: false
proxy:
  host: 0.0.0.0
  port: 30000
  served_model_name: skillclaw-model
skills:
  dir: /opt/data/.skillclaw/skills
  enabled: true
```

## Problema conhecido com PRM

O SkillClaw tenta inicializar o PRMScorer mesmo quando PRM está desabilitado no config, causando erro se não houver API key. **Solução:** garantir que `prm.enabled: false` no config.yaml.

## Daemon

```bash
skillclaw start --daemon
skillclaw status
skillclaw stop
```

## SkillClaw + Hermes Integration

Ao escolher `hermes` como CLI agent, o SkillClaw:
1. Usa `~/.hermes/skills` como diretório de skills
2. Reescreve o `~/.hermes/config.yaml` para apontar o Hermes ao proxy local
3. Copia skills legadas de `~/.skillclaw/skills` para `~/.hermes/skills`

## Status Atual

- SkillClaw instalado e rodando em `/opt/data/skillclaw_install/SkillClaw/`
- Daemon ativo (PID variável) na porta 30000
- `claw_type: hermes` configurado
- Proxy responde em `http://127.0.0.1:30000/healthz`