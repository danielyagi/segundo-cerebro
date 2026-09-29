# Vellum Assistant — Instalação e Migração (Jarvis → Alfred)

**Data:** 29/09/2026
**Modelo:** openai/gpt-6-luna (OpenRouter)
**VPS:** Jarvis (i3a1, 76.13.29.94) → Alfred (bc6r, 179.198.101.163)

## O que foi feito

### Instalação no Jarvis
- Imagem construída a partir do repositório oficial (`vellum-assistant:local`), commit `cf6b942da93ece77f3852ef15d838c87725149d4`
- Container iniciado e confirmado em execução: 582 MiB, zero reinícios, sem OOM
- 5 volumes Docker persistentes criados:
  - `vellum-assistant_ces-bootstrap`
  - `vellum-assistant_ces-data`
  - `vellum-assistant_ces-security`
  - `vellum-assistant_vellum-home`
  - `vellum-assistant_vellum-workspace`
- Rede interna: `vellum-assistant-internal` (bridge, não publicada)
- Compose em: `/docker/vellum-assistant/docker-compose.yml`

### Configuração do Provedor (OpenRouter + GPT-6 Luna)
- Credential Execution Service configurado com armazenamento criptografado em volume persistente
- Chave OpenRouter (do próprio env da VPS) cadastrada como provedor
- Conexão `openrouter-luna` criada
- Perfil `jarvis-gpt6-luna` configurado
- OpenRouter selecionado como provedor padrão do Vellum
- Teste real de inferência: GPT-6 Luna respondeu `VELLUM_OPENROUTER_OK`

### Rede
- Interna: rede Docker bridge privada sem exposição externa da API
- Saída à internet necessária para o OpenRouter; conectado também a rede Docker de saída
- Compose alterado para manter essa configuração entre reinícios

### Estado do Jarvis (antes da migração)
- **Uptime:** 13 dias
- **RAM:** 7.8 GB total, ~2.2 GB usado, 5.5 GB disponível
- **Disco:** 96 GB, 38 GB usado (40%)
- **vCPUs:** 2
- **Swap:** nenhum
- **Containers:** Hermes, 9Router, Firefox, Traefik, Browserless, Vellum
- **Hermes:** 1.45 GiB, config `unless-stopped`, sem limites de CPU/memória

## Pendente — Migração para o Alfred

**Status:** NÃO concluída — iniciada mas interrompida (a sessão terminou antes da conclusão)

### Estado atual do Alfred
- **Uptime:** 22 dias
- **RAM:** 31 GB total, ~5.8 GB usado, 25 GB disponível
- **Disco:** 387 GB, 64 GB usado (17%)
- **Containers:** Traefik (host network, sem Dashboard API), Hermes, Ollama (2 instâncias), Evolution API, Chrome, Browserless
- **Traefik:** `exposedbydefault=false`, Let's Encrypt via HTTP-01, sem labels de domínio para serviços
- **Chave OpenRouter:** presente em `/docker/hermes-agent-bc6r/data/.env` (73 chars)

### Passos necessários para completar
1. Preparar `/docker/vellum-assistant/` no Alfred com docker-compose.yml
2. Construir imagem local do Vellum (ou puxar do Jarvis)
3. Transferir volumes de dados via rsync entre VPS (ou tar via SFTP)
4. Configurar Traefik labels para expor Vellum via dashboard Hostinger
5. Testar inferência com GPT-6 Luna
6. Remover deployment do Jarvis

### Observações
- Traefik no Alfred está em `network_mode: host` — ao contrário do Jarvis, não há proxy reverso configurado com labels Docker para o Vellum
- Para acesso via dashboard Hostinger, será necessário expor porta no container e configurar Traefik ou publicar porta diretamente
- Volumes do Vellum no Jarvis contêm credenciais criptografadas — migração cuidadosa necessária para evitar perda de dados