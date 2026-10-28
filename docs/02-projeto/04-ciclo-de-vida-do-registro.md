# Ciclo de vida do registro

Fase 2 (Projeto). Atualizado em 27/10/2026, com as respostas do autor. Documento vivo.

**O que é:** o caminho que um relato percorre desde que o usuário o envia até virar dado final, incluindo os desvios quando algo dá errado e o fim da vida do dado. Para cada etapa: o que o sistema faz e o que o usuário vê.

**Origem do conteúdo:** a maior parte vem das decisões do repositório antigo (marcadas **[herdado]**). O que é novo está marcado **[novo]**. Se cada estado vira uma coluna no banco ou é calculado fica para o modelo de dados.

Stories relacionadas: US-01, US-02, US-03, US-06, US-11, US-12, RNF-01, RNF-02.

---

## Decisões tomadas (27/10/2026)
1. **O relato é gravado na hora.** A confirmação é informativa e traz um botão **Desfazer**. Não há botão "Confirmar".
2. **Pergunta pendente é respondida por botão.** Texto digitado enquanto há pergunta pendente é tratado como novo relato, e a pergunta expira.
3. **Validade:** 30 minutos para a pergunta pendente e também para a janela de contexto da US-02. Ao expirar, nada é gravado como série, o texto bruto fica salvo e o bot não inicia conversa. O valor é ajustável depois da primeira semana de teste.
4. **Retenção:** o dado fica enquanto o usuário existir no sistema, e só sai por exclusão a pedido (US-12). Pode ser usado para análise de padrões do teste, e isso precisa constar no aviso da boas-vindas (US-11). Antes de abrir além dos testadores aprovados, a retenção é revista: separar o que fica (séries estruturadas) do que sai (texto bruto) ou anonimizar.
5. **Mantido do repositório antigo:** `/desfazer` com exclusão lógica, pilha de 24 horas, observação que não desfaz e reprocessamento fora do MVP.

---

## 1. Tipos de mensagem
Toda mensagem de um usuário aprovado é guardada crua. Ela pode ser:
- **Relato:** texto de treino, que passa pelo extrator.
- **Consulta** (ex.: `/carga supino`): guardada crua, sem sessão, não é atingida pelo `/desfazer`. [herdado]
- **Comando** (`/desfazer`, `/start`): tratado pelo bot, sem extrator. [herdado]
- Mensagens de quem não está ATIVO, mensagens em grupo e edições de mensagem não entram no ciclo: são ignoradas ou recebem um aviso, e nada é gravado. [herdado]

O resto deste documento trata do **relato**.

## 2. Estados do relato

| Estado | Significado | O usuário vê |
|---|---|---|
| **RECEBIDO** | Texto bruto salvo; extração ainda não terminou. É momentâneo. | Nada |
| **AGUARDANDO_RESPOSTA** [novo] | O bot perguntou algo que não dá para assumir (ex.: qual exercício) e espera até 30 minutos. | A pergunta, com botões de resposta |
| **REGISTRADO** | Séries salvas; sessão criada ou atualizada. | A confirmação com data, sessão, exercício, séries, `~` e `—`, e o botão **Desfazer** |
| **SEM_SERIES** [herdado] | O relato não tinha séries. Com observação, cria ou atualiza a sessão; sem observação, não cria sessão. | "Não encontrei séries" (ou "anotei") |
| **NAO_REGISTRADO** [herdado] | Falha depois de salvar o texto (extração, API, validação, observação cheia). O texto bruto continua salvo. | "Salvei o texto, mas não consegui registrar. Reenvie." |
| **EXPIRADO** [novo] | A pergunta pendente não foi respondida em 30 minutos. Nada foi gravado como série; o texto bruto fica salvo. | Nada (o bot não inicia conversa) |
| **DESFEITO** [herdado] | O usuário desfez o registro. É marcado (com data), nunca apagado. | A lista do que saiu |

Pendência de vínculo [novo]: se o exercício do relato não está no catálogo, a **série** fica registrada, com o exercício oficial vazio ("não catalogado"), e é religada quando o usuário confirmar. É um atributo da série, não um estado do relato.

## 3. Transições

1. **Chegou → RECEBIDO.** O texto bruto é salvo e confirmado no banco **antes** de qualquer chamada à API. Se falhar aqui, nada foi salvo e o Telegram reentrega. [herdado]
2. **RECEBIDO → REGISTRADO.** Extração, validação em Java e gravação de sessão + séries numa transação só (tudo ou nada). [herdado]
3. **RECEBIDO → AGUARDANDO_RESPOSTA.** Falta um dado que o bot não pode assumir. Nada é gravado como série até a resposta. [novo]
4. **AGUARDANDO_RESPOSTA → REGISTRADO.** O usuário toca num botão dentro de 30 minutos; a série entra com a resposta. [novo]
5. **AGUARDANDO_RESPOSTA → EXPIRADO.** Passam 30 minutos, ou o usuário envia texto novo. [novo]
6. **RECEBIDO → NAO_REGISTRADO.** Qualquer falha depois do texto salvo. É um resultado, não uma exceção, para a reentrega não cair em "já gravada" e ficar sem resposta. [herdado]
7. **REGISTRADO → DESFEITO.** Pelo botão **Desfazer** ou pelo comando `/desfazer`. [herdado, com o botão novo]

### Botão Desfazer e comando `/desfazer`
- O **comando** `/desfazer` atinge a última mensagem válida com registro nas últimas 24 horas, e cada novo `/desfazer` atinge a seguinte. [herdado]
- O **botão** [novo] desfaz exatamente o registro da confirmação em que aparece, não "o último". Vale pelo mesmo prazo de 24 horas; depois disso, o bot responde que o prazo passou.
- Os dois são idempotentes: tocar duas vezes no botão, ou o Telegram reentregar o comando, não desfaz um segundo registro. [herdado para o comando]

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
- **Exclusão a pedido (US-12):** o administrador apaga mensagens, sessões e séries do usuário; a cópia do backup também é tratada. Prazo e método ainda são [definir] na US-12.
- **Uso para análise:** os dados podem ser analisados para entender padrões de uso do teste, mesmo de quem parou de usar, desde que o aviso da boas-vindas diga isso. A exclusão a pedido continua valendo para todos.
- **Revisão antes de abrir além dos testadores:** decidir o que fica (séries estruturadas) e o que sai (texto bruto, que é o mais sensível), ou anonimizar. Isto não é parecer jurídico.
- Mensagens `NAO_REGISTRADO` e `EXPIRADO` ficam como estão; **reprocessar** é pós-MVP. O usuário reenvia. [herdado]

## 6. Pontos abertos
1. Prazo e método da exclusão (US-12), incluindo o backup.
2. Ajustar os 30 minutos depois da primeira semana de teste.
3. Revisão da retenção antes de abrir além dos testadores.
