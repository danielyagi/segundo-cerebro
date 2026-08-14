# Custom Provider 9router no Hermes — Configuração

**Data:** 14/08/2026
**Contexto:** Configuração de custom provider `9router` como default no Hermes Agent
**Tags:** hermes, provider, custom-provider, 9router, config

## Estrutura no config.yaml

Custom providers são definidos na seção `custom_providers` com:
- `name`: Identificador do provider
- `base_url`: Endpoint da API (ex: `https://9router-elit.srv1668986.hstgr.cloud/v1`)
- `api_key`: Chave da API
- `model`: Modelo default do provider
- `models`: Lista de modelos disponíveis

```yaml
custom_providers:
  - name: 9router-elit.srv1668986.hstgr.cloud
    base_url: https://9router-elit.srv1668986.hstgr.cloud/v1
    api_key: sk-...
    model: gemini/gemini-3.1-flash-lite-preview
    models:
      - hermes
      - nvidia/deepseek-ai/deepseek-v4-flash
      - nvidia/deepseek-ai/deepseek-v4-pro
      # ... etc
```

## Comandos CLI

```bash
# Setar provider como default
hermes config set model.provider '9router-elit.srv1668986.hstgr.cloud'

# Setar modelo default
hermes config set model.default 'gemini/gemini-3.1-flash-lite-preview'

# Limpar entradas antigas (se provider antigo usava openrouter)
hermes config unset model.api_key
hermes config unset model.base_url
```

## Observações

- O nome do custom provider no `model.provider` deve bater exatamente com o `name:` do custom_providers
- `model.api_key` e `model.base_url` no topo são para provider **não custom** — para custom provider esses campos ficam dentro do bloco custom_providers
- Fallback providers ainda podem ser configurados via `fallback_providers` ou `fallback_model`

## Modelos Disponíveis no 9router (parcial)

- gemini/gemini-3.1-flash-lite-preview
- nvidia/deepseek-ai/deepseek-v4-flash
- nvidia/deepseek-ai/deepseek-v4-pro
- nvidia/minimaxai/minimax-m2.7
- nvidia/minimaxai/minimax-m3
- nvidia/moonshotai/kimi-k2.6
- nvidia/nemotron-3-ultra-550b-a55b
- bpm/seed-2-0-pro-260328
- kimchi/kimi-k2.7