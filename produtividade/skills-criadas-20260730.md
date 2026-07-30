# Skills Criadas - 30/07/2026

## Skills Criadas no Hermes

| Skill | Categoria | Descrição |
|---|---|---|
| `project-dependency-management` | devops | Gerencia dependências do package.json, npm audit, workspaces |
| `browser-testing-with-jsdom-linkedom` | testing | Testes de DOM com jsdom/linkedom em Node.js |
| `hermes-workspace-maintenance` | devops | Manutenção de workspaces do monorepo (web, ui-tui, apps) |
| `skillclaw-integration` | software-development | Gerenciamento do proxy SkillClaw e integração com Hermes |
| `notebooklm-automation` | software-development | Automação de NotebookLM via Playwright |

## Skill Loopy

- Repositório oficial: `https://github.com/Forward-Future/loopy`
- Instalado em `/opt/hermes/.agents/skills/loopy/` (movido para `/opt/data/agents/skills/loopy/`)
- Skill para gerenciar loops de agentes autônomos (descobrir, auditar, criar, executar)
- **Nota:** O Hermes busca skills em `/opt/data/agents/skills/` - skills em diretórios com ponto (`.agents/`) não aparecem no `skills_list`

## Considerações sobre Skills

- Skills com ponto no nome do diretório (`.agents/`) não são detectadas pelo `skills_list`
- A pasta de skills reconhecida é `/opt/data/agents/skills/`
- O diretório `/opt/hermes/` tem restrições de permissão - usar `/opt/data/` para escrita