# Processo e método

Fase 1 (Planejamento). Este documento registra como o Atlas será desenvolvido e por quê. Cada decisão traz a escolha, o motivo e a alternativa descartada. É um documento vivo: se o processo mudar, atualiza-se aqui.

## Contexto

Projeto individual, sem cliente fixo e sem equipe. O autor também quer aprender o ciclo completo de um projeto de software, não só programar. Os requisitos ainda vão mudar com o uso real, então o processo precisa aceitar mudança sem perder o controle.

## Decisão 1: modelo de ciclo de vida

- **Escolha:** ciclo evolutivo. O produto cresce em incrementos pequenos, e cada incremento é testado com pessoas reais antes do próximo.
- **Motivo:** os requisitos vêm de uma pesquisa pequena e serão corrigidos pelo teste. O primeiro incremento é o MVP; os seguintes (áudio, site, evolução em tela) só começam depois de o anterior ser validado.
- **Descartado:**
  - Cascata: exige requisitos fechados antes do uso real, e aqui eles ainda são hipóteses.
  - Espiral: traz ciclos formais de análise de risco, que pesam mais do que o projeto de uma pessoa comporta.

## Decisão 2: gestão do trabalho

- **Escolha:** Kanban, com fluxo contínuo e limite de 1 a 2 tarefas em andamento.
- **Motivo:** uma única pessoa, tempo irregular e escopo que muda. Kanban não exige iterações de duração fixa e evita abrir várias frentes ao mesmo tempo.
- **Descartado:** Scrum, porque seus papéis, cerimônias e sprints foram pensados para equipes.
- **Regras:**
  - O trabalho passa por etapas: ideias/futuro, a fazer, em andamento, em teste e feito.
  - Só se puxa uma nova tarefa quando há vaga no limite.
  - Uma tarefa está pronta quando os critérios de aceite da story foram cumpridos e o documento afetado foi atualizado.

## Decisão 3: documentação antes do código

- **Escolha:** cada fase produz seus documentos antes da fase seguinte começar. Não se escreve código na fase de planejamento nem no projeto.
- **Motivo:** o autor precisa entender e conseguir explicar cada parte do sistema. Documentar primeiro obriga a decidir antes de construir.
- **Consequência:** uma mudança de escopo ou de requisito atualiza o documento correspondente na mesma tarefa em que a mudança acontece.

## Decisão 4: ligação entre documentos, tarefas e código

- **Escolha:** cada tarefa nasce de uma user story ou de uma decisão registrada e cita o documento de origem. Cada alteração de código cita a tarefa a que pertence.
- **Motivo:** permite seguir o caminho inverso, do código até o requisito que o justifica.
- **Observação:** as ferramentas que sustentam essa ligação serão escolhidas na fase de Projeto.

## Decisão 5: registro de decisões

- **Escolha:** toda decisão com impacto duradouro (escopo, modelo de dados, tecnologia, arquitetura) é registrada com contexto, decisão, alternativas consideradas e consequências. Os registros ficam junto dos documentos do projeto.
- **Motivo:** daqui a meses será difícil lembrar por que algo foi escolhido. O registro evita rediscutir decisões já tomadas e mostra o raciocínio.

## Como as fases se encaixam

1. Planejamento: origem da ideia, levantamento de requisitos, user stories, processo e método, escopo do MVP.
2. Projeto: casos de uso, modelo de dados, arquitetura e tecnologias.
3. Construção: implementação do MVP, uma tarefa por vez.
4. Validação: teste de aceitação com usuários reais.
5. Entrega e 6. Sustentação.

Concluído o MVP, cada incremento seguinte repete o ciclo em tamanho menor: story, ajuste de projeto, construção e teste.

## Quando revisar

- Ao fim de cada incremento: rever stories, escopo e este documento.
- Sempre que o teste com usuários contradisser uma hipótese: atualizar o documento afetado e registrar a decisão.
