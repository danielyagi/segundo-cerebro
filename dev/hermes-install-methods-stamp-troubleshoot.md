# Hermes — Stale `.install_method` Stamp Bloqueando Update

**Data:** 01/09/2026
**Sessão:** @session:default/20260901_194612_2d3557c2
**Tags:** hermes, update, install_method, stamp, troubleshoot, devops

## Cenário

Hermes Agent v0.20.0 (2026.8.3) instalado em `/opt/hermes` como editable install (bare-metal, não Docker). Ao executar `hermes update`, o sistema recusa com:

```
✗ ``hermes update`` doesn't apply inside the Docker container.
```

## Causa Raiz

O arquivo `/opt/hermes/.install_method` contém `docker`. Esse stamp foi **baked in na build da imagem Docker** e persistiu mesmo após a instalação bare-metal.

O Hermes usa **dois stamps** para detectar o método de instalação:

1. **Code-scoped** (`/opt/hermes/.install_method`) — verificado primeiro, retorna imediatamente se existe
2. **Home-scoped** (`$HERMES_HOME/.install_method`) — usado como fallback

A função `stamp_install_method()` no `hermes_cli/config.py` (linha ~522) **silenciosamente no-ops** (`except OSError: pass`) se o diretório for read-only — então mesmo que quisesse corrigir, não consegue.

## Árvore de Diagnóstico

```
hermes update --check → "doesn't apply inside Docker container"
  └─ detect_install_method() encontra stamp "docker" em /opt/hermes/.install_method
      └─ /opt/hermes/ é root:root, sem permissão de escrita pro user hermes
          ├─ sem sudo disponível
          ├─ sem .git/ em /opt/hermes (impossível git pull)
          └─ upstream github.com/NousResearch/hermes-agent retorna 404
              └─ repositório pode ter mudado de localização
```

## Soluções Possíveis

1. **Root access:** `rm /opt/hermes/.install_method` seguido de `git init + git remote add + hermes update`
2. **Forçar reinstall:** `uv pip install --force-reinstall hermes-agent` no venv `/opt/hermes/.venv`
3. **Community:** Verificar qual o repositório upstream correto (github.com/NousResearch/hermes-agent deu 404)

## Notas

- `git --version`: 2.47.3 (disponível)
- `uv` instalado (v0.11.6)
- PyPI latest: 0.19.0 (mais antigo que o local 0.20.0)
- Processo gateway roda como `hermes` user (uid 10000)
- Container detectado: `/.dockerenv` existe, faz `_running_in_container()` retornar True (mas é host bare-metal com ambiente containerizado)