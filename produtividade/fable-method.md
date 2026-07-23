# Produtividade com o Fable Method

## Instalação e Conceito (23/07/2026)

O Fable Method foi instalado como 4 skills no Hermes. É uma metodologia que pode ser aplicada a qualquer tarefa não-trivial para melhorar a qualidade do trabalho através de estruturação em passos e verificação adversarial.

### Quando usar
- Tarefas multi-passo não-triviais
- Trabalho que será executado sem supervisão (unattended)
- Após qualquer agente/tool afirmar que o trabalho está concluído (rodar `/fable-judge`)
- Para domínios especializados (marketing, pesquisa, dados, finanças, etc.)

### Princípio Central
> "Um modelo de nível médio seguindo o loop vence um modelo mais forte que improvisa: a qualidade está na estrutura, na evidência e na honestidade, não no modelo."

### Regra de Ouro
- Antes de qualquer tarefa multi-passo não trivial: aplicar o loop do fable-method
- Depois de trabalho concluído: rodar fable-judge para verificar
- Para tarefas unattended ou com subagentes: usar fable-loop