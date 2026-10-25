# Escopo do MVP

Fase 1 (Planejamento). Este documento define o que o primeiro incremento do Atlas cobre, o que fica de fora e como saberemos se o teste deu certo. É um documento vivo: mudanças de escopo são registradas aqui e em `decisoes/`.

Documentos de origem: `02-levantamento-de-requisitos.md`, `03-user-stories.md`, `04-processo-e-metodo.md`.

## Objetivo do MVP

Verificar se praticantes conseguem registrar o treino por conversa, em texto, sem atrapalhar o treino, e se voltam a usar o registro nas semanas seguintes.

O MVP não pretende ensinar a importância de anotar o treino. Se o teste mostrar que as pessoas passam a anotar, isso será consequência observada, não meta.

## Hipóteses que o teste verifica

- H1: registrar por texto livre é viável durante o treino. Risco: na pesquisa, botões foram a forma preferida (7 de 20), à frente do texto (5 de 20).
- H2: a dor de não ter histórico existe para quem treina com progressão de carga. A pesquisa não comprovou isso (ver `02-levantamento-de-requisitos.md`).
- H3: quem registra consulta a evolução por comando.

## Quem usa

Praticantes que treinam com ficha ou acompanham a carga, convidados e aprovados um a um pelo administrador (o autor), todos maiores de 18 anos. O MVP não é aberto ao público nem a clientes de academia.

O recrutamento prioriza esse perfil, mesmo que sejam poucas pessoas. O número mínimo de testadores com esse perfil é [definir]. O autor não conta nas métricas.

## O que o MVP faz

| Story | O que entra |
|---|---|
| US-01 | Relatar o treino em linguagem natural, por texto |
| US-02 | Relatar aos poucos, com o bot guardando o contexto entre mensagens |
| US-03 | Ver o que o bot entendeu, com botões para confirmar |
| US-04 | Consultar a última carga de um exercício |
| US-06 | Desfazer o último registro, com botão |
| US-07 | Entrar no teste sem criar conta |
| US-08 | Administrador aprova testadores |
| US-09 | Respostas acolhedoras, sem cobrança |
| US-10 | Limites claros para mensagens fora do assunto |
| US-11 | Saber o que é gravado |
| US-12 | Pedir a exclusão dos dados |
| US-17 | Drop set como uma única série com etapas |
| US-19 | Consultar a evolução por comando (uso medido no teste) |
| RNF-01 a RNF-05 | Confiar no histórico, não perder nem duplicar, privacidade e segurança, respeitar o orçamento, responder rápido |

Forma de interação: texto livre para relatar o treino; botões apenas para confirmar, desfazer e resolver dúvidas do bot.

## O que fica de fora

| Item | Motivo |
|---|---|
| US-05 Perguntas em texto livre | Épico grande, ainda sem critérios |
| US-13 Áudio | Só depois de o texto estar estável |
| US-14 Evolução em tela de celular | Depende de validar o chatbot |
| US-15 Site explicativo | Depende de validar o chatbot |
| US-16 Uso sem ficha (iniciante) | Exige catálogo genérico e tom próprio |
| Fotos e vídeos | Fora do canal de texto do MVP |
| Sugestão de carga e dúvidas de treino | Visão do produto, não do primeiro incremento |
| Bi-set e tri-set | Adiados; drop set cobre a técnica mais citada |
| Abertura para alunos de academia | Exige revisão de privacidade antes (ver riscos) |
| Lembretes de registro | Ideia futura, sem teste |

## Como saberemos se deu certo

Critérios do teste, com números ainda por definir antes de começar:

- Quantos testadores aprovados registraram pelo menos um treino na primeira semana: [definir].
- Quantos continuaram registrando na segunda semana: [definir].
- Parte dos relatos confirmada sem correção: [definir]. Confirmar sem corrigir pode refletir pressa; por isso, uma amostra de relatos de tamanho [definir] é conferida manualmente contra o que o testador disse.
- Uso do comando de evolução (US-19): [medir], para decidir se a tela de evolução vale o esforço.
- Percepção dos testadores sobre o tom do bot (US-09): [coletar].
- Custo da API no período de teste dentro do orçamento (RNF-04).

## Premissas e limites

- A amostra da pesquisa é pequena e de conveniência; as preferências do formulário são hipóteses que o teste confirma ou contradiz.
- O bot nunca inventa dados que o usuário não falou (RNF-01).
- Tecnologias e arquitetura são escolhidas na fase de Projeto, não aqui.

## Riscos

- **Privacidade:** relatos podem trazer dado de saúde (dor, lesão), que a LGPD trata como sensível, já durante o teste. Na Fase 2, decidir o que se grava, por quanto tempo e como o consentimento é pedido. Antes de abrir para além dos testadores aprovados, revisar o aviso de privacidade. Isto não é parecer jurídico.
- **Extrator:** os relatos reais variam muito (faixas de repetição, carga vaga, erros de digitação). Pode ser preciso ajustar muito.
- **Dor não comprovada:** a pesquisa não mostrou que o problema existe para quem treina com progressão de carga, e 65% dos respondentes não registram o treino hoje. O teste pode mostrar que o problema não é a ferramenta. Antes do teste, conversas curtas com quem aceitou falar (ver `02`).
- **Texto contra botões:** a pesquisa favoreceu botões. Se os testadores abandonarem o texto livre, a aposta de H1 falhou.
- **Viés de cortesia e novidade:** testadores conhecidos tendem a usar por gentileza, e o interesse cai depois das primeiras semanas. Métricas só da 2ª semana em diante, sem contar o autor.
- **Indisponibilidade do bot:** se o serviço estiver desligado (por exemplo, o computador que o hospeda), a retenção medida fica distorcida. Registrar os períodos fora do ar.
- **Dependência do uso de IA:** custo e disponibilidade da API afetam o teste.

## Decisões pendentes

- US-18 (separar aquecimento): entra ou vai para o Pós-MVP? Só 2 de 20 anotam aquecimento.
- Valores dos critérios de sucesso acima.
