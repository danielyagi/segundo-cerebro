# Docker e Browserless na VPS Hostinger

**Data:** 11-12/08/2026
**Contexto:** Configuração do Docker na VPS Hostinger para rodar Browserless e conectar ao Hermes Agent

## Setup do Docker

### Inicialização

O Docker estava instalado mas o daemon não estava rodando. Para iniciar:

```bash
sudo dockerd
```

### Rede Personalizada

Para comunicação entre containers, criar uma rede compartilhada:

```bash
docker network create hermes-net
```

### Executar Browserless

```bash
docker run -d \
  --name browserless \
  --network hermes-net \
  -p 127.0.0.1:8080:80 \
  browserless/chrome
```

- `-p 127.0.0.1:8080:80` → expõe apenas localmente (segurança)
- Browserless fica acessível em `ws://127.0.0.1:8080` (WebSocket) ou `http://127.0.0.1:8080` (HTTP)

### Conectando Hermes ao Browserless

Se o Hermes usa Playwright:

```python
from playwright.sync_api import sync_playwright
BROWSERLESS_URL = "ws://127.0.0.1:8080/ws"
with sync_playwright() as p:
    browser = p.chromium.connect(BROWSERLESS_URL)
    page = browser.new_page()
    page.goto("https://example.com")
```

Se o Hermes usa CDP diretamente:
- Endpoint: `http://127.0.0.1:8080` (ou `ws://127.0.0.1:8080/ws`)

## Problemas Conhecidos

- **Docker daemon não inicia automaticamente** na VPS Hostinger — iniciar manualmente com `sudo dockerd`
- **Browserless + Hermes fora do Docker:** acessar via `127.0.0.1:8080` (porta mapeada)
- **Browserless + Hermes em container separado:** usar a rede `hermes-net` e nome do container como hostname

## Comandos Úteis

```bash
# Verificar status
docker info

# Listar containers
docker ps -a

# Ver logs do Browserless
docker logs browserless

# Parar/remover
docker stop browserless && docker rm browserless

# Redes disponíveis
docker network ls
```