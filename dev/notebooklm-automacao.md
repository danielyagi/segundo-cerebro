# NotebookLM - Automação via MCP Server

## Data: 29/08/2026 (atualizado)

## Contexto

O usuário (perito judicial Daniel Hideo Yagi) deseja integrar o Hermes Agent com o Google Gemini Notebook (formerly NotebookLM) para ter acesso a notebooks e usar como RAG.

## Solução Recomendada: `notebooklm-mcp-cli`

**Projeto:** `jacob-bd/gemini-notebook-mcp-cli` — 5.9k ⭐, 921 forks, v0.10.0
**Licença:** MIT | **Python:** >=3.11 | **Instalação:** `uv tool install notebooklm-mcp-cli`

Este projeto substitui completamente a abordagem anterior de browser automation (Playwright + Browserless), sendo mais eficiente e estável.

### O que é

CLI + MCP Server unificado que expõe **43 tools** de acesso programático ao Google Gemini Notebook via APIs internas RPC (batchexecute). Tem suporte explícito ao Hermes Agent (`nlm skill install hermes`).

### Arquitetura

```
src/notebooklm_tools/
├── core/          # Cliente base HTTP + auth
│   ├── base.py    # Chamadas RPC para batchexecute do Google
│   ├── auth.py    # Extração de cookies via CDP
│   └── client.py  # NotebookLMClient thread-safe
├── services/      # Lógica de negócio
│   ├── notebooks.py, sources.py, chat.py
│   ├── studio.py, research.py, sharing.py
│   ├── notes.py, downloads.py, exports.py
│   └── errors.py  # ServiceError, ValidationError, etc.
├── cli/           # CLI commands (thin wrapper)
└── mcp/           # FastMCP server + 43 tools
    ├── server.py  # FastMCP facade
    └── tools/     # Tools modulares por domínio
```

**Protocolo:** Google batchexecute interno
```
POST https://notebooklm.google.com/_/LabsTailwindUi/data/batchexecute
f.req = [["rpc_id", params_json, null, "generic"]]
```

### As 43 Tools MCP

| Categoria | Tools |
|---|---|
| **Notebooks** (6) | `notebook_list`, `get`, `describe`, `create`, `rename`, `delete` |
| **Sources** (7) | `source_add` (URL/texto/file/Drive), `list_drive`, `sync_drive`, `delete`, `describe`, `get_content`, `rename` |
| **Chat/Query** (4) | `notebook_query`, `query_start`, `query_status`, `chat_configure` |
| **Chats** (3) | `chat_list`, `chat_get`, `chat_export` |
| **Studio** (4) | `studio_create` (audio/video/infographic/slides/flashcards/report/quiz), `status`, `delete`, `revise` |
| **Research** (3) | `research_start`, `status`, `import` |
| **Sharing** (4) | `notebook_share_public`, `invite`, `status`, `batch` |
| **Downloads** (2) | `download_artifact`, `download_all_artifacts` |
| **Exports** (1) | `export_artifact` (Google Docs/Sheets) |
| **Notes** (1) | `note` (CRUD unificado) |
| **Collections** (5) | `collection_list`, `create`, `edit`, `set_emoji`, `delete` |
| **Labels** (1) | `label` (auto/list/reorganize/create/rename/set_emoji/move_source/delete) |
| **Smart Select** (1) | `tag` (add/remove/list/select) |
| **Auth** (2) | `refresh_auth`, `save_auth_tokens` |
| **Batch** (1) | `batch` (query/add_source/create/delete) |
| **Cross-notebook** (1) | `cross_notebook_query` |
| **Pipeline** (1) | `pipeline` (run/list workflows multi-step) |
| **Server** (1) | `server_info` |

### Auth

- **Cookies obrigatórios** — extraídos via `nlm login` (abre Chromium gerenciado localmente, captura cookies via CDP)
- **CSRF token + Session ID** — auto-extraídos do primeiro request (não precisa fornecer)
- **Cache:** `~/.notebooklm-mcp-cli/profiles/default/auth.json`
- **Cookies estáveis por semanas** — CSRF/Session auto-refresh em cada inicialização
- **Fallback:** env vars `NOTEBOOKLM_COOKIES`, `NOTEBOOKLM_CSRF_TOKEN`, `NOTEBOOKLM_SESSION_ID`

### Integração no Hermes

```yaml
# config.yaml
mcp_servers:
  gemini-notebook:
    command: "notebooklm-mcp"
    env:
      NOTEBOOKLM_HL: "pt-BR"
    timeout: 120
```

```bash
# Instalação
uv tool install notebooklm-mcp-cli
nlm login                        # Autenticação única
nlm skill install hermes         # Registra skill-guia
```

### Riscos e Limitações

1. **Auth via navegador local** — `nlm login` requer Chromium na máquina. Não recomendado para VPS/cloud em produção (Google pode expirar sessão)
2. **APIs internas não oficiais** — Google pode quebrar a API. Mitigação: RPC drift detection + `NOTEBOOKLM_RPC_OVERRIDES` env var
3. **Single-account** — Um perfil Google por processo MCP
4. **File paths locais** — Upload/download referem-se ao filesystem do host MCP

### Detecção de RPC Drift

Quando o Google rotaciona IDs de método RPC (ex: `wXbhsf` → novo ID):
- Cliente lança `RPCDriftError` com os novos IDs detectados
- Rode com `--debug` para ver `RPC IDs in response: [...]`
- Hot-patch via env var: `NOTEBOOKLM_RPC_OVERRIDES='{"RPC_LIST_NOTEBOOKS": "<new_id>"}'`
- Auto-retry em `RESOURCE_EXHAUSTED` (RPC error code 8) com exponential backoff

### Histórico (abordagem anterior — descontinuada)

**Abordagem antiga (30/07/2026):** Browser Harness + Playwright + Browserless para scraping do NotebookLM. Substituída pelo MCP server que usa APIs internas diretamente, sem necessidade de browser automation.

Infraestrutura Browserless mantida para outros usos (automação judicial, scraping geral).

### Status

- ✅ `notebooklm-mcp-cli` analisado e recomendado
- ✅ 43 tools MCP mapeadas
- ✅ Arquitetura e auth documentados
- ✅ Integração Hermes documentada
- ⏳ Pendente: Instalação + `nlm login` + config.yaml update