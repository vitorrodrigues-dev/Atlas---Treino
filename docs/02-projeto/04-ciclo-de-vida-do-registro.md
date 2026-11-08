# Ciclo de vida do registro

Fase 2 (Projeto). Atualizado em 08/11/2026 pela revisão de consistência (reprocessamento do D19, motivo ERRO_INTERNO, conteúdo que não é texto); antes, em 01/11/2026, com a decisão 2 de 28/10 aceita pelo autor e o veto do botão Desfazer fechado. Documento vivo.

**O que é:** o caminho que um relato percorre desde que o usuário o envia até virar dado final, incluindo os desvios quando algo dá errado e o fim da vida do dado. Para cada etapa: o que o sistema faz e o que o usuário vê.

**Origem do conteúdo:** a maior parte vem das decisões do repositório antigo (marcadas **[herdado]**). O que é novo está marcado **[novo]**. Se cada estado vira uma coluna no banco ou é calculado fica para o modelo de dados.

Stories relacionadas: US-01, US-02, US-03, US-06, US-11, US-12, RNF-01, RNF-02. Casos de uso: UC-01, UC-02, UC-03, UC-09 (`05-casos-de-uso.md`).

---

## Decisões tomadas (27/10/2026, ajustadas em 01/11/2026)
1. **O relato é gravado na hora.** A confirmação é informativa e traz um botão **Desfazer**. Não há botão "Confirmar".
2. **Série ambígua ou desconhecida também é gravada na hora, sem exercício** (decisão 2 de 28/10, aceita em 01/11). O bot mostra botões com as candidatas e o toque só **vincula** a série a um exercício. Não existe pergunta pendente que expira: o relato nunca fica retido esperando resposta. O botão de vínculo vale enquanto a série existir e não estiver desfeita, sem prazo. Texto digitado depois é um novo relato normal. [novo]
3. **Janela de contexto:** 30 minutos, usada **só** na US-02 para decidir a qual exercício atribuir uma série que não cita exercício ("2ª série, 55kg"). Se o último exercício citado está fora da janela, a série é gravada pendente de vínculo, como em 2: com botões quando houver candidatas (até 5, D15); sem candidatas, a série fica pendente sem botões. [novo] O valor é ajustável depois da primeira semana de teste.
4. **Retenção:** o dado fica enquanto o usuário existir no sistema, e só sai por exclusão a pedido (US-12). Pode ser usado para análise de padrões do teste, e isso precisa constar no aviso da boas-vindas (US-11). Antes de abrir além dos testadores aprovados, a retenção é revista: separar o que fica (séries estruturadas) do que sai (texto bruto) ou anonimizar.
5. **Mantido do repositório antigo:** `/desfazer` com exclusão lógica, pilha de 24 horas, observação que não desfaz e reprocessamento de NAO_REGISTRADO fora do MVP.
6. **Botão Desfazer aprovado** (28/10): ele **soma** ao `/desfazer`, não o substitui.

---

## 1. Tipos de mensagem
Toda mensagem de um usuário ATIVO é guardada crua. Ela pode ser:
- **Relato:** texto de treino, que passa pelo extrator.
- **Consulta** (ex.: `/carga supino`): guardada crua, sem sessão, não é atingida pelo `/desfazer`. [herdado]
- **Comando** (`/desfazer`, `/start`): tratado pelo bot, sem extrator. [herdado]
- Mensagens de quem não está ATIVO, mensagens em grupo, edições de mensagem e qualquer conteúdo que não seja texto (foto, vídeo, áudio, figurinha, documento) [novo] não entram no ciclo: são ignoradas ou recebem um aviso, e nada é gravado. [herdado]

O resto deste documento trata do **relato**.

## 2. Estados do relato

| Estado | Significado | O usuário vê |
|---|---|---|
| **RECEBIDO** | Texto bruto salvo; extração ainda não terminou. É momentâneo. Se o serviço parar antes de terminar, na subida ele grava `reprocessado_em` antes da chamada externa e reprocessa **uma vez** os relatos RECEBIDO que ainda não têm essa marca; se falhar, o relato vira NAO_REGISTRADO. Um RECEBIDO que já tem a marca (o serviço caiu de novo no meio) vira NAO_REGISTRADO, motivo ERRO_INTERNO. Nos dois casos o usuário recebe o aviso de reenviar (A12 de `07-arquitetura.md`, D19 de `06-modelo-de-dados.md`). [novo] | Nada |
| **REGISTRADO** | Séries salvas; sessão criada ou atualizada. Pode haver séries pendentes de vínculo. | A confirmação com data, sessão, exercício, séries, `~` e `—`, o botão **Desfazer** e, se houver pendência, os botões de vínculo |
| **SEM_SERIES** [herdado] | O relato não tinha séries. Com observação, cria ou atualiza a sessão; sem observação, não cria sessão. | "Não encontrei séries" (ou "anotei") |
| **NAO_REGISTRADO** [herdado] | Falha depois de salvar o texto (extração, API, validação, observação cheia, erro interno [novo]). O texto bruto continua salvo. | "Salvei o texto, mas não consegui registrar. Reenvie." |
| **DESFEITO** [herdado] | O usuário desfez o registro. É marcado (com data), nunca apagado. No modelo de dados é a data `desfeita_em`, não um valor de `resultado`; o comportamento é o mesmo. | A lista do que saiu |

**Pendência de vínculo** [novo]: se o exercício do relato não está no catálogo, ou o nome é ambíguo, a **série** é gravada com o exercício oficial vazio ("pendente de vínculo") e é ligada quando o usuário tocar no botão (UC-02). É um atributo da série, não um estado do relato, e não tem prazo de expiração. Nunca se cria exercício oficial automaticamente.

## 3. Transições

1. **Chegou → RECEBIDO.** O texto bruto é salvo e confirmado no banco **antes** de qualquer chamada à API. Se falhar aqui, nada foi salvo e o Telegram reentrega. [herdado]
2. **RECEBIDO → REGISTRADO.** Extração, validação no código e gravação de sessão + séries numa transação só (tudo ou nada). Séries cujo exercício não pode ser assumido entram pendentes de vínculo. [herdado, ajustado]
3. **RECEBIDO → NAO_REGISTRADO.** Qualquer falha depois do texto salvo, inclusive erro inesperado ao gravar (motivo ERRO_INTERNO, D19 [novo]). É um resultado, não uma exceção, para a reentrega não cair em "já gravada" e ficar sem resposta. [herdado]
4. **REGISTRADO → DESFEITO.** Pelo botão **Desfazer** ou pelo comando `/desfazer`. [herdado, com o botão novo]
5. **Série pendente → vinculada.** O usuário toca no botão do exercício correto (UC-02). Tocar duas vezes é idempotente. Se a série foi desfeita antes, o bot avisa que o registro não vale mais. [novo]

### Botão Desfazer e comando `/desfazer`
- O **comando** `/desfazer` atinge a última mensagem válida com registro nas últimas 24 horas, e cada novo `/desfazer` atinge a seguinte. [herdado]
- O **botão** desfaz exatamente o registro da confirmação em que aparece, não "o último". Vale pelo mesmo prazo de 24 horas; depois disso, o bot responde que o prazo passou. [novo, aprovado]
- Os dois são idempotentes: tocar duas vezes no botão, ou o Telegram reentregar o comando, não desfaz um segundo registro. [herdado para o comando]
- Desfazer uma mensagem desfaz também as séries pendentes de vínculo dela.

## 4. Regras que valem em todas as transições [herdado]
- **Idempotência:** o mesmo relato reentregue pelo Telegram não grava duas vezes (a reentrega é silêncio). Enviar o mesmo texto de novo, como mensagem nova, **cria** outro registro, e o Desfazer cobre isso.
- **Tudo ou nada:** uma série inválida derruba a mensagem inteira; o texto fica salvo.
- **Nunca trunca nem arredonda** dado do usuário; texto maior que a coluna é recusado.
- **A observação do dia não é desfeita:** ela é concatenada na sessão e não dá para saber de qual mensagem veio. Reenviar com a mesma observação a duplica (defeito cosmético aceito).
- **Mensagens desfeitas não aparecem** em nenhuma leitura: `/carga`, evolução (US-19) e o resto.
- **Edição de mensagem no Telegram é ignorada;** o bot avisa e ensina o Desfazer.
- **Texto bruto e dados nunca vão para log;** só código, classe e duração (RNF-03).

## 5. Fim da vida do dado
- **Desfeito:** fica no banco, marcado, para preservar o histórico e a métrica de correções. Sai do banco só junto com a exclusão do usuário.
- **Exclusão a pedido (US-12, UC-09):** o administrador apaga mensagens, sessões e séries do usuário; a cópia do backup também é tratada. Prazo de 7 dias corridos; método em D2 do `06-modelo-de-dados.md`.
- **Uso para análise:** os dados podem ser analisados para entender padrões de uso do teste, mesmo de quem parou de usar, desde que o aviso da boas-vindas diga isso. A exclusão a pedido continua valendo para todos.
- **Revisão antes de abrir além dos testadores:** decidir o que fica (séries estruturadas) e o que sai (texto bruto, que é o mais sensível), ou anonimizar. Isto não é parecer jurídico.
- Mensagens `NAO_REGISTRADO` ficam como estão; **reprocessar** é pós-MVP. O usuário reenvia. [herdado] O reprocessamento na subida do serviço (A12) vale só para relatos RECEBIDO e não muda isto. [novo]

## 6. Pontos abertos
1. Prazo e método da exclusão (US-12), incluindo o backup. **Fechado em 07/11/2026:** D2 do `06-modelo-de-dados.md`.
2. Ajustar os 30 minutos da janela de contexto depois da primeira semana de teste. **Fechado em 08/11/2026:** 30 minutos, contados pelo envio da mensagem (D21 do `06-modelo-de-dados.md`, decidido por delegação do autor); rever depois da primeira semana é ajuste normal, não pendência.
3. Revisão da retenção antes de abrir além dos testadores.
4. Conflito apontado na revisão de 28/10: o texto livre da série segue a série ou o texto bruto na revisão de retenção? **Fechado em 07/11/2026:** segue o texto bruto (D2 do `06-modelo-de-dados.md`).
5. O que fazer quando nenhuma candidata do botão de vínculo é o exercício certo (UC-02, 1c). **Fechado em 07/11/2026:** D8 do `06-modelo-de-dados.md`.