# NotebookLM - Automação e Alternativas

## Data: 30/07/2026

## Contexto

O usuário (perito judicial Daniel Hideo Yagi) deseja integrar o Hermes Agent com o Google NotebookLM para ter acesso a notebooks e usar como RAG (Retrieval-Augmented Generation).

## Descobertas

1. **Não existe API oficial do NotebookLM** - O Google não expõe endpoints públicos ou SDK para o NotebookLM.
2. **Repositório `notebooklm-py`** - Existe no GitHub mas é uma tentativa não-oficial da comunidade, frágil (scraping).
3. **Melhor abordagem:** Reproduzir a funcionalidade de RAG localmente com arquivos do usuário.

## Alternativa Implementada

### Browser Harness + Playwright + Browserless

Em vez de depender de scrapers não-oficiais, foi criada uma infraestrutura de automação de navegador na VPS:

1. **Browserless (Chrome headless):** Servidor rodando na VPS Hostinger (`browserless/chrome` Docker)
2. **Playwright:** Instalado em venv dedicado `/opt/data/browser-harness-env/`
3. **Conexão validada:** Script de teste navegou com sucesso até a página de documentação da LinuxServer via VPS

### Arquitetura

```
Hermes Agent → Script Python → Playwright → WebSocket → Browserless (VPS) → Navegador Chrome
```

## Como Usar

```python
from playwright.async_api import async_playwright
import os

BROWSER_USE_CDP_URL = os.getenv("BROWSER_USE_CDP_URL")
# ws://<host>:3000

async with async_playwright() as p:
    browser = await p.chromium.connect_over_cdp(BROWSER_USE_CDP_URL)
    page = await browser.new_page()
    await page.goto("https://exemplo.com")
    content = await page.content()
```

## Status

- ✅ Infraestrutura VPS validada
- ✅ Playwright instalado no venv
- ✅ Conexão WebSocket funcional
- ⏳ Pendente: Criar scripts de automação para sites específicos (judiciais, etc.)