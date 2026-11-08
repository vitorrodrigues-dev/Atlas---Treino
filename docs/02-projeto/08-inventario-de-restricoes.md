# Inventário de restrições

Fase 2 (Projeto). **Rascunho de 07/11/2026, redigido com assistência de IA (Claude). Ainda não lido nem aceito pelo autor.** Documento vivo.

É a entrada da escolha de tecnologias: junta o que limita as opções, com números. **Não escolhe nada.** Fontes: `07-arquitetura.md` (seção 8), `06-modelo-de-dados.md`, `03-user-stories.md`, `05-escopo-do-mvp.md`, o `progresso.md` do repositório antigo e pesquisa externa de 07/11/2026 (lista no fim).

## Como ler

Cada linha tem um **status**:

- **V**: fato verificado em fonte externa em 07/11/2026.
- **D**: declarado pelo autor em documento do projeto.
- **E**: estimativa. As premissas estão escritas.
- **?**: só o autor responde (perguntas na seção 9).

Valores que mudam com o tempo (preços, limites de plano) precisam ser conferidos de novo na hora de contratar.

## 1. Canal de mensagens (Telegram, hipótese herdada)

| Restrição | Valor | Status | Efeito |
|---|---|---|---|
| Tamanho do texto enviado numa mensagem | até 4096 caracteres | V | Confirmação longa é resumida (arquitetura §9) |
| Dado carregado por um botão (`callback_data`) | até 64 **bytes**, não caracteres | V | O botão leva só identificadores numéricos (série, exercício, mensagem), nunca nomes |
| Tempo que o canal guarda o que o bot não buscou | **até 24 horas** | V | Serviço fora do ar por mais de 24 h **perde relatos**. É o teto real do "notebook desligado" |
| Formas de receber mensagens | consulta periódica ou chamada de volta, mutuamente exclusivas | V | Chamada de volta exige endereço público com HTTPS; consulta periódica não exige |
| Confirmação de recebimento na consulta periódica | uma atualização é confirmada quando se pede a seguinte com identificador maior | V | Serve ao A1 (confirmar só depois de T1), desde que o processamento seja em ordem (A8) |
| Edição de mensagem e mídia | chegam como tipos de evento próprios | V | A17 (aviso, nada gravado) |
| Canal futuro: WhatsApp | atrás da porta do Canal | D | A9 |

## 2. Serviço de interpretação (modelo de linguagem)

| Restrição | Valor | Status | Efeito |
|---|---|---|---|
| Modelo candidato | Claude Haiku 5.5, id `claude-haiku-5-5`, lançado em 07/10/2026 | V | O prompt v3 foi validado no Haiku 4.5; trocar de modelo exige a regressão (A18) |
| Preço até 100 mil tokens de prompt | US$ 0,10 por milhão de entrada e US$ 0,50 por milhão de saída (Haiku 4.5: US$ 1 e US$ 5) | V | O custo por relato cai cerca de 10 vezes |
| Prompt acima de 100 mil tokens | preço de entrada e saída 5 vezes maior | V | Irrelevante: o prompt do Atlas é pequeno |
| Custo medido no repositório antigo (Haiku 4.5) | ≈ US$ 0,003 por extração | D | Base da estimativa abaixo |
| Custo estimado por relato no Haiku 5.5 | ≈ US$ 0,0003, supondo a mesma contagem de tokens | E | Medir no spike: a contagem pode variar entre modelos |
| Custo do teste | 15 testadores × 14 dias × 5 relatos por dia ≈ 1050 relatos: ≈ US$ 0,32 (Haiku 5.5) ou ≈ US$ 3,15 (Haiku 4.5) | E (premissas minhas) | O custo da API deixa de ser o fator que decide; reprocessamentos (A12) e a regressão somam pouco |
| Orçamento declarado | até R$ 50 por mês para APIs; crédito pré-pago de US$ 5 sem recarga automática | D | Folga larga. Não está dito se hospedagem entra nesse valor (pergunta 1) |
| Recusa pelo modelo | uma fonte secundária diz que o Haiku 5.5 não tem alternativa do lado do servidor quando os classificadores de segurança recusam | V parcial | **Verificar na documentação oficial.** Recusa deve virar EXTRACAO_FALHOU; relatos de dor ou emergência podem disparar |
| Versão fixa do modelo | o apelido do modelo muda sozinho; usar identificador de versão e temperatura 0 | D | Existe identificador datado para o Haiku 5.5? **Verificar** |
| Saída estruturada | exigida pela arquitetura (§8, item 6) | D | Conferir o suporte no modelo escolhido |
| O texto do relato passa pela API externa | consta do aviso de dados | D (US-11) | Local de processamento do provedor: ver seção 7 |

## 3. Banco de dados

| Restrição | Valor | Status | Efeito |
|---|---|---|---|
| Banco hoje | Oracle da FIAP | D | Até quando existe o acesso e se há limites: pergunta 2 |
| Meta declarada | banco próprio, independente da FIAP | D | Escolha entre Oracle, Postgres e outros vira registro de decisão |
| Candidato citado: Oracle Cloud Always Free (Autonomous Database) | no máximo 2 instâncias por conta e 30 sessões simultâneas | V | Volume do Atlas cabe com folga |
| Mesmo candidato, backup | **backup e restauração só em instância paga** | V | A política de 30 dias (D2) viraria exportação manual agendada |
| Mesmo candidato, inatividade | a documentação de um fórum Oracle diz que o banco para depois de 7 dias sem atividade e é removido depois de 3 meses parado | V parcial | Confirmar. Não afeta o teste, mas afeta um banco esquecido depois |
| Volume esperado | ≈ 1050 relatos e alguns milhares de séries no teste | E | Capacidade não é restrição |
| O que o modelo exige | transações, unicidade (R1 a R3, R7), intervalos (R16), chave composta ou verificação equivalente (R4, R5) | D | Checklist da arquitetura §8, item 1 |
| Backup | cópias com no máximo 30 dias; exclusão refeita se restaurar | D (D2) | Item 2 da checklist |

## 4. Hospedagem e execução

| Restrição | Valor | Status | Efeito |
|---|---|---|---|
| Hospedagem no teste | notebook do autor (16 GB de RAM), ligado, custo zero | D | Teto de queda de 24 h (seção 1) |
| Cuidados no notebook | suspensão desativada e atualizações adiadas durante o teste | D | Registrar início e parada do serviço (A13) |
| Intenção para depois | servidor próprio (VPS Debian com contêiner) | D | Preço e fornecedor: pergunta 1 |
| Estabilidade do local (energia e conexão) | não registrada | ? | Pergunta 3 |
| Segredos | só em variáveis de ambiente, fora do repositório | D (RNF-03) | Itens 5 e 9 da checklist |

## 5. Tempo e pessoas

| Restrição | Valor | Status | Efeito |
|---|---|---|---|
| Quem constrói e mantém | uma pessoa | D | Peso grande em ferramentas simples e conhecidas |
| Horas por dia | cerca de 2 | D | Define o tamanho das fatias |
| Cronograma declarado | construção em cerca de 3 meses, férias a partir de dezembro, teste de aceitação em janeiro | D | **Os dois podem não caber:** pergunta 4 |
| Prioridade sobre o Atlas | candidaturas e faculdade vêm antes | D | O Atlas fica nos horários livres |

## 6. Competências do autor

| Fato | Status | Efeito |
|---|---|---|
| Java com orientação a objetos e JDBC (repositório antigo), Python com Flask, HTML e CSS | D | Opções fora disso têm custo de aprendizado dentro do prazo |
| JavaScript, TypeScript e front-end são lacuna | D | Pesa contra qualquer pilha com muito front-end no MVP |
| A faculdade cobre Spring Boot, React e TypeScript nesta fase | D | Spring Boot dá reaproveitamento entre faculdade e projeto |
| Intenção declarada: Spring Boot com API REST | D (24/10) | Hipótese a validar contra este inventário |
| Meta: dominar o ciclo de projeto e saber defender o que está no repositório | D | Prefere o que ele consegue ler e explicar a ganhos de produtividade que ele não domina |

## 7. Privacidade e conformidade

| Restrição | Status | Efeito |
|---|---|---|
| Relatos podem trazer dado de saúde (dor, lesão), sensível pela LGPD | D | Aviso na boas-vindas, exclusão a pedido (D2), revisão antes de abrir além dos testadores. Isto não é parecer jurídico |
| O texto passa por API de terceiro; local de processamento do provedor não verificado | ? | Verificar e escrever no aviso (US-11). Pergunta 5 |
| Repositório público: nada de segredo, token nem dado pessoal | D (RNF-03) | Segredos fora do repositório; conferência antes de cada commit |
| Testadores maiores de 18 anos, aprovados um a um | D | Sem abertura ao público no MVP |

## 8. O que mais restringe a escolha

1. **O canal só guarda mensagens por 24 horas.** Hospedagem em notebook é aceitável no teste, mas uma queda longa perde relatos.
2. **Competência do autor.** Java e Spring Boot têm a maior chance de o autor defender o resultado.
3. **Backup.** O Oracle Always Free não faz backup nativo, então qualquer escolha de banco precisa de rotina manual de exportação.
4. **O custo da API não decide.** O que decide o custo é a hospedagem, e ela ainda não tem número.
5. **Recusas do modelo.** A mitigação (EXTRACAO_FALHOU e lista de regressão) já existe na arquitetura; falta verificar o comportamento real.

## 9. Perguntas que só o autor responde

1. O orçamento de R$ 50 por mês inclui hospedagem? Se houver servidor, qual o teto?
2. Até quando existe o acesso ao Oracle da FIAP, e pode ser usado no teste inteiro?
3. O notebook fica ligado e conectado durante os 14 dias? Há risco de queda de energia ou de conexão que passe de 24 horas?
4. O cronograma (construção em cerca de 3 meses e teste em janeiro) cabe? Quando o teste começa de fato?
5. Aceita deixar a verificação do local de processamento do provedor para antes de abrir além dos testadores, ou quer verificar antes do teste?

## Fontes (pesquisa de 07/11/2026)

- Telegram, Bot API: https://core.telegram.org/bots/api
- Limites de `callback_data` e de texto: wiki do python-telegram-bot (https://github.com/python-telegram-bot/python-telegram-bot/wiki/Arbitrary-callback_data) e documentação de cliente (https://github.com/TundraSoft/tundra-connect/wiki/Telegram-API)
- Preço do Claude Haiku 5.5: https://techenclave.com/news/items/claude-haiku-5-5-launches-with-0-10-input-pricing-for-shorter-prompts, https://mixed-news.com/en/?p=35955 e https://dev.to/hassann/claude-haiku-55-pricing-53cb
- Oracle Always Free Autonomous Database: https://docs.oracle.com/en-us/iaas/Content/GSG/Concepts/alwaysfree.htm e https://forums.oracle.com/ords/apexds/post/apex-database-instance-on-the-oci-free-tier-inexplicably-st-4475
