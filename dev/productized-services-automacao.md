# Automação de Productized Services com Agentes de IA

> Conhecimento extraído em 31/07/2026 — Estratégia de implementação com Hermes + Jarvis

## Stack de Automação para Serviços Periciais

### Componentes Principais
- **Hermes Agent:** Orquestrador de agentes autônomos, execução de tarefas em segundo plano
- **Jarvis:** Interface de voz e interação com cliente, notificações
- **Browserless/Playwright:** Automação de navegador para acesso a tribunais (e-SAJ, Projudi)
- **NotebookLM / RAG:** Base de conhecimento para consulta de processos e jurisprudência

### Integração com Ferramentas Existentes
- **Automatização de Fluxos:** Hermes para automatizar etapas do processo, da entrada de dados à geração de relatórios
- **Comunicação com Clientes:** Jarvis para gerenciar interações, agendar serviços e enviar atualizações
- **Gestão de Assinaturas:** Sistema integrado com Hermes e Jarvis para cobranças automáticas e monitoramento

## Considerações Técnicas

### Segurança e Confiabilidade
- Implementar criptografia e acesso controlado para dados sensíveis
- Nunca persistir credenciais de tribunais (e-SAJ, Projudi) em texto plano
- Usar SSH para autenticação Git, tokens clássicos apenas para setup inicial

### Treinamento de Clientes
- Desenvolver materiais educativos para ajudar clientes a aproveitar os serviços automatizados
- Documentar o fluxo de entrega do "serviço-produto"

## Lições Aprendidas

1. **Não vender o software, vender o resultado:** O valor está no laudo/relatório entregue, não na ferramenta em si
2. **Margem alta com custo quase zero:** Uma vez configurado, o custo computacional de cada execução é mínimo comparado ao valor cobrado
3. **Escalar sem contratar:** A automação permite multiplicar a capacidade sem aumentar a equipe
4. **Foco na dor do advogado:** O que mais agrega valor é a celeridade — entregar em horas o que levaria dias