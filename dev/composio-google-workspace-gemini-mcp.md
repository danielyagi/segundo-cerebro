# Composio: Integração Google Workspace + Gemini + MCP (12/10/2026)

## Contexto
Conectar o ambiente Hermes (Alfred/Containers) ao Composio para permitir chamadas de ferramentas que invoquem Google Workspace, Gemini, GitHub, Vercel e Claude Code. Configurado primeiro no ambiente local (`/opt/data`) antes de replicar no Alfred.

## Pacote correto (crítico)
- **`composio`** (novo, SDK modular) → versão atual `0.26.0`.
- **`composio-core`** (antigo) → `0.7.21`, **DEPRECATED** e quebrado.
- Sintoma do pacote antigo:
  `Error: {"error":"This endpoint is no longer available. Please upgrade to v3 APIs."}`
- Instalação: `uv pip install composio` dentro do venv `/opt/data/.venv`.

## Fluxo padrão do SDK novo (sessões)
```python
from composio import Composio
c = Composio(api_key="ak_...")
s = c.sessions.create(user_id="alfred_test_user")
res = s.execute("COMPOSIO_SEARCH_TOOLS", arguments={"query": "gmail"})
```
- Assinatura validada:
  - `Composio(provider=None, **kwargs)`
  - `sessions.create(user_id=..., mcp=False, ...)`
  - `sessions.use(session_id, *, custom_tools=None, custom_toolkits=None, mcp=False)`
- APIs `sessions`, `tool_router`, `mcp`, `tools` existem no SDK novo.
- Obter MCP server:
  ```python
  server = c.mcp.get("6b11a932-...")  # .mcp_url, .name
  ```
  Nota: `client.mcp.retrieve` está deprecated desde 2026-08-28; sucessor é `https://backend.composio.dev/api/v3/tool_router/session`.

## Integrações validadas (read-only primeiro)
- **Gmail**: conectado → `GMAIL_GET_PROFILE` → `dan.yagi@gmail.com`, `messagesTotal: 20538`, `threadsTotal: 16804`.
- **Google Drive / Calendar / Sheets**: conectados via Connect Links.
- **Gemini ("Google Spark")**: conectado → `GEMINI_GENERATE_IMAGE` retornou `generated_image.png` (storage temporário).
- **Google "Spark" no setup** = toolkit `googlesuper` (camada unificada do Workspace): tools `GOOGLESUPER_FIND_FILE`, `CREATE_DOCUMENT_MARKDOWN`, `FETCH_EMAILS`, `LIST_CALENDARS`, `CREATE_EVENT`, `SEND_EMAIL`, `UPDATE_DOCUMENT_SECTION_MARKDOWN`. **Não** há um scheduler separado "Spark".
- **GitHub + Vercel (`vercel_mcp`)**: toolkits disponíveis; ligados via Connect Links. Hostinger NÃO aparece como toolkit Composio (VPS é fora do catálogo).
- **Google Apps Script**: não há toolkit dedicado; acesso indireto via Google Drive (`GOOGLEDRIVE_EXPORT_GOOGLE_WORKSPACE_FILE` exporta `.gs`/Apps Script para JSON). Para gerenciar/deploy direto, usar caminho nativo do Apps Script, não Composio.

## Autenticação dos apps externos
- Fluxo de **Connect Link** (`https://connect.composio.dev/link/lk_...`) para autorizar Gmail/Drive/Calendar/Sheets/GitHub/Vercel.
- É comum encontrar primeiro `<toolkit> "not yet connected"` e precisar gerar o link e aguardar o usuário autorizar.

## Gemini CLI no Alfred (concluído e depois descartado)
- Node.js atualizado; `@google/gemini-cli@0.63.0` instalado; Composio MCP server adicionado ao `~/.gemini/settings.json`:
  ```json
  { "mcpServers": { "composio": { "url": "https://backend.composio.dev/v3.1/mcp/<ID>", "type": "http", "trust": true } } }
  ```
- **Blocker final**: Gemini CLI lista MCP "Disabled" em pasta não-trusted, e `--skip-trust` cai na exigência de auth (`GEMINI_API_KEY` / `GOOGLE_GENAI_USE_VERTEXAI` / `GOOGLE_GENAI_USE_GCA`).
- **Decisão do usuário**: **pular/pular Gemini CLI** (não quer fazer essa etapa). Manter apenas o Composio via Hermes, sem insistir no CLI.

## Lições / decisões
- Sempre testar integrações **read-only primeiro** (perfil, listagem) antes de ações destrutivas.
- Troca `composio-core` → `composio` foi a decisão técnica crítica.
- Não persistir credenciais/API keys em texto plano em arquivos do repo.