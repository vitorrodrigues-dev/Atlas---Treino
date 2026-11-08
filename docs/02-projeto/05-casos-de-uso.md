# Casos de uso

Fase 2 (Projeto). Redigido em 01/11/2026 com assistência de IA (Claude) a partir de `03-user-stories.md`, `01-definicoes-de-dominio.md`, `04-ciclo-de-vida-do-registro.md` e das decisões de 28/10. **Lido e aceito pelo autor em 01/11/2026**, com as decisões listadas no fim. Documento vivo.

## Como ler

- Um caso de uso descreve a interação entre um ator e o sistema, passo a passo, sem citar tecnologia. Os fluxos alternativos são o que alimenta o modelo de dados depois (estados, campos, restrições).
- Cada UC tem: ator, pré-condição, gatilho, fluxo principal, fluxos alternativos (numerados pelo passo que desviam: 3a, 4b), pós-condição.
- Regras de negócio não são repetidas aqui: o UC aponta para a story, o RNF ou a definição de domínio onde estão.
- `[definir]` = falta decisão. `[PREMISSA]` = ponto herdado de uma decisão ainda sem página em `decisoes/`.
- Atores: **Testador** (praticante aprovado), **Administrador** (o autor), **Serviço de interpretação** (a IA que extrai os dados do relato; ator secundário, externo). O canal é o chat do Telegram (restrição herdada, revista na etapa de tecnologias).
- **Fronteira do sistema (decidida em 01/11/2026):** o banco de dados está dentro. O script do administrador é a interface dele com o sistema.

## Visão geral

| UC | Nome | Ator primário | Stories |
|---|---|---|---|
| UC-01 | Registrar relato de treino | Testador | US-01, 02, 03, 17, 18, RNF-01, RNF-02 |
| UC-02 | Vincular exercício pendente | Testador | US-02, decisões de catálogo |
| UC-03 | Desfazer registro | Testador | US-06 |
| UC-04 | Consultar última carga | Testador | US-04 |
| UC-05 | Consultar evolução | Testador | US-19 |
| UC-06 | Solicitar entrada | Pessoa interessada | US-07, US-11 |
| UC-07 | Aprovar testador | Administrador | US-08 |
| UC-08 | (rebaixado a regra RT-01) | | US-10 |
| UC-09 | Excluir dados a pedido | Administrador | US-12 |
| UC-10 | Cadastrar a ficha do testador | Administrador | lacuna da revisão de 28/10 |

---

## UC-01 Registrar relato de treino

- **Ator primário:** Testador. **Secundário:** Serviço de interpretação.
- **Pré-condição:** o testador está ATIVO.
- **Gatilho:** o testador envia uma mensagem de texto que não é comando.

**Fluxo principal**
1. O testador envia o texto relatando o treino.
2. O sistema guarda o texto original.
3. O sistema envia o texto ao serviço de interpretação, que devolve exercícios, séries, cargas, repetições e observações.
4. O sistema valida o resultado. O que o testador não disse fica vazio, nunca preenchido por suposição (RNF-01).
5. O sistema identifica a sessão: a do dia, ou uma nova se o testador pediu ("segundo treino").
6. O sistema grava as séries e a observação na sessão, tudo ou nada.
7. O sistema envia a confirmação com data, sessão, exercícios e séries (`~` no aproximado, `—` no que não foi dito), o tipo das séries que não são de trabalho, as etapas de um drop set e o botão **Desfazer**.

**Fluxos alternativos**
- 1a. A mensagem está fora do assunto (xingamento, dieta, medicação, emergência): o sistema responde conforme **RT-01** e nada é registrado como treino.
- 1b. A mesma mensagem é entregue duas vezes: o sistema ignora a repetição, sem resposta nova.
- 2a. O texto não pôde ser guardado: nada foi registrado; a mensagem é processada quando o sistema voltar (RNF-02).
- 3a. O serviço de interpretação falha: o texto continua guardado e o sistema avisa "salvei o texto, mas não consegui registrar; reenvie".
- 4a. A validação recusa (valor fora do intervalo, texto grande demais): tudo ou nada, nenhuma série é gravada, o texto continua guardado e o testador é avisado.
- 4b. O relato não tem séries: se tem observação, cria ou atualiza a sessão e responde que anotou; se não tem, não cria sessão e responde que não encontrou séries.
- 5a. O testador diz "segundo treino" ou "treino da noite": o sistema abre uma nova sessão no mesmo dia.
- 6a. O exercício não está no catálogo do testador: a série é gravada com o exercício oficial vazio ("não catalogado"), a confirmação diz "Não reconheci: ..." e a série fica pendente de vínculo (UC-02). Nunca se cria exercício oficial automaticamente.
- 6b. O nome pode ser vários exercícios da ficha ("remada"): a série é gravada pendente e a confirmação traz botões com as candidatas (UC-02). *(Decisão 2, aceita em 01/11/2026.)*
- 6c. A mensagem não cita exercício ("2ª série, 55kg"): se o último exercício citado está dentro da janela de 30 minutos, a série vai para ele e a confirmação diz qual foi assumido; senão, vale 6b.
- 6d. Relato de drop set: uma única série com etapas, cada uma com carga e repetições, na ordem dita (US-17). Carga não dita fica vazia, nunca copiada da etapa anterior.
- 6e. O testador disse aquecimento ou preparatória: a série recebe esse tipo. Aquecimento geral (esteira, mobilidade) vai para a observação da sessão (US-18).
- 6f. O relato traz dor ou lesão: o sistema anota sem diagnosticar, a confirmação sugere procurar um profissional de saúde e não recomenda o que fazer com a carga (RT-01).

**Pós-condição de sucesso:** o relato está registrado em uma sessão, o texto original está guardado e o testador recebeu a confirmação.
**Pós-condição de falha:** o texto original continua guardado, nenhuma série foi gravada e o testador sabe disso.

**Regras:** "NxM", carga, tipo de série, falha e texto livre estão em `01-definicoes-de-dominio.md`; estados do relato em `04-ciclo-de-vida-do-registro.md` (atualizado em 01/11/2026 pela decisão 2).

---

## UC-02 Vincular exercício pendente

- **Ator primário:** Testador.
- **Pré-condição:** existe uma série pendente de vínculo, que não foi desfeita. *(Decisão 2, aceita em 01/11/2026: a série é gravada na hora, sem exercício; não há espera nem expiração.)*
- **Gatilho:** o testador toca em um botão de candidata na confirmação.

**Fluxo principal**
1. O testador toca no botão do exercício correto.
2. O sistema liga a série a esse exercício.
3. O sistema responde dizendo a qual exercício a série foi vinculada.

**Fluxos alternativos**
- 1a. O testador toca duas vezes, ou o toque é entregue duas vezes: a segunda vez não muda nada (idempotente).
- 1b. A série foi desfeita antes do toque: o sistema avisa que o registro não vale mais.
- 1c. Nenhuma candidata é o exercício certo: a série continua pendente e o testador não precisa fazer nada; o administrador acrescenta o exercício à ficha pelo UC-10. Não há correção de vínculo no MVP: vínculo errado se corrige com **Desfazer** e reenvio. *(Decidido em 07/11/2026.)*
- 1d. O botão só vincula enquanto a série está pendente; depois disso o sistema responde a qual exercício ela já está ligada.
- 2a. **Apelido (decidido em 01/11/2026):** se o texto do relato não casou com nenhum exercício (caso 6a), o vínculo cria um apelido do testador com esse texto, em minúsculas, apontando para o exercício escolhido. Se o texto era ambíguo (caso 6b, como "remada"), **nenhum apelido é criado**, porque nome ambíguo não vira apelido e o bot volta a perguntar da próxima vez.

**Pós-condição:** a série está ligada a um exercício oficial do catálogo do testador, e ele sabe qual.

---

## UC-03 Desfazer registro

- **Ator primário:** Testador.
- **Pré-condição:** o testador está ATIVO e existe um registro válido nas últimas 24 horas.
- **Gatilho:** toca no botão **Desfazer** de uma confirmação, ou envia `/desfazer`.

**Fluxo principal (comando)**
1. O testador envia `/desfazer`.
2. O sistema seleciona a mensagem válida mais recente do testador, com registro, nas últimas 24 horas (consultas não contam).
3. O sistema marca essa mensagem e as séries dela como desfeitas, sem apagar nada.
4. O sistema responde listando o que saiu (data, exercícios, número de séries) e avisando se a sessão tem observação, que não é desfeita.

**Fluxo principal (botão)**
1. O testador toca em **Desfazer** na confirmação.
2. O sistema desfaz exatamente o registro daquela confirmação.
3. O sistema responde como no passo 4 do comando.

**Fluxos alternativos**
- 2a. Não há registro válido nas últimas 24 horas: o sistema avisa que não há nada a desfazer.
- 1a. O Telegram entrega o mesmo comando duas vezes, ou o botão é tocado duas vezes: nenhum segundo registro é desfeito.
- 1b. O botão é de um registro com mais de 24 horas: o sistema avisa que o prazo passou.
- 3a. A mensagem desfeita tinha séries pendentes de vínculo: elas saem junto, e os botões de vínculo dela deixam de valer.

**Pós-condição:** o registro está marcado como desfeito (com data), preservado para o histórico e para a métrica de correções, e não aparece mais em nenhuma leitura (`/carga`, evolução).

**Fora do MVP (decidido em 01/11/2026):** o desfazer é sempre da mensagem inteira, sem pedir confirmação. Desfazer parcial (por série ou por exercício) e um botão **Refazer** ficam para depois do teste, conforme o uso medido (quantas vezes se desfaz e por quê). O que o testador perde ao desfazer é só o trabalho de reenviar o relato, porque o texto original continua guardado.

---

## UC-04 Consultar última carga

- **Ator primário:** Testador.
- **Pré-condição:** o testador está ATIVO.
- **Gatilho:** envia `/carga` seguido do nome ou apelido do exercício.

**Fluxo principal**
1. O testador envia `/carga <exercício>`.
2. O sistema guarda a consulta (sem sessão; `/desfazer` não a atinge).
3. O sistema identifica o exercício pelo apelido, pelo nome oficial ou por todas as palavras do texto.
4. O sistema localiza a sessão mais recente **do próprio testador** com séries desse exercício, ignorando registros desfeitos.
5. O sistema responde com a data e todas as séries dessa sessão, na ordem em que foram registradas, com o tipo marcado nas que não são de trabalho e `~`/`—` onde couber. A carga aparece como o usuário disse (valor, forma declarada e `~`), não a normalizada.

**Fluxos alternativos**
- 3a. O nome serve para mais de um exercício: o sistema lista as opções em vez de escolher.
- 3b. Nenhum exercício corresponde: o sistema avisa que não reconheceu.
- 3c. O testador não informou o exercício: o sistema orienta o uso do comando.
- 4a. O testador nunca registrou esse exercício: o sistema avisa que não há registro.

**Pós-condição:** o testador viu a última carga de um exercício dele, sem que nada tenha sido alterado.

---

## UC-05 Consultar evolução

- **Ator primário:** Testador.
- **Pré-condição:** o testador está ATIVO.
- **Gatilho:** envia `/evolucao` seguido do nome ou apelido do exercício. *(Nome decidido em 01/11/2026; sem acento porque o Telegram não aceita acento em comando.)*

**Fluxo principal**
1. O testador envia `/evolucao <exercício>`.
2. O sistema identifica o exercício como no UC-04.
3. O sistema reúne as séries de **trabalho** do exercício, ignorando aquecimento, preparatórias e registros desfeitos.
4. O sistema responde com a carga de trabalho por data, da mais antiga à mais recente, **sem filtro de período** (todas as sessões do exercício).

**Fluxos alternativos**
- 2a. Nome ambíguo ou não reconhecido: como UC-04 (3a, 3b).
- 3a. Há menos de **3** sessões com carga de trabalho do exercício: o sistema avisa que ainda não há dados suficientes.
- 4a. Para cada sessão, a maior carga normalizada entre as séries de trabalho (etapa 1), com as repetições da série em que ocorreu. Séries sem carga normalizada ficam de fora. Se houver unidades incomparáveis (kg e placa), mostra só a mais recente e avisa.

**Pós-condição:** o testador viu a evolução; o uso do comando é contado para a hipótese H3.

*(Decidido em 01/11/2026: sem período e mínimo de 3 registros, como ponto de partida. Revisar depois da primeira semana de teste.)*

---

## UC-06 Solicitar entrada

- **Ator primário:** Pessoa interessada (ainda não cadastrada). **Secundário:** Administrador (recebe o aviso).
- **Pré-condição:** a pessoa abriu o bot.
- **Gatilho:** envia `/start`.

**Fluxo principal**
1. A pessoa envia `/start`.
2. O sistema cadastra a pessoa como PENDENTE, com nome, identificador do canal e data. Nunca grava o texto da mensagem. **[PREMISSA: decisão 5 de 28/10, sem página em `decisoes/`]**
3. O sistema envia a boas-vindas com o aviso de dados: grava texto e treinos; é um banco de estudo; o texto passa pela API da Anthropic; pode pedir para apagar; o acesso depende de aprovação (US-11). O sistema registra a data do envio do aviso.
4. O sistema avisa o administrador (UC-07).

**Fluxos alternativos**
- 1a. A pessoa já está PENDENTE: o sistema responde "aguardando aprovação" e avisa o administrador de novo.
- 1b. A pessoa já está ATIVA: o sistema responde "já cadastrado".
- 1c. A pessoa está BLOQUEADA: silêncio total.
- 1d. A pessoa não cadastrada envia qualquer coisa que não seja `/start`: silêncio. Nada de quem não está ATIVO é guardado.

**Pós-condição:** a pessoa está PENDENTE e leu o aviso de dados.

---

## UC-07 Aprovar testador

- **Ator primário:** Administrador.
- **Pré-condição:** há um testador PENDENTE e o administrador recebeu o aviso (nome, identificador interno e o comando `/aprovar` pronto).
- **Gatilho:** o administrador envia `/aprovar <id>`.

**Fluxo principal**
1. Fora do sistema, o administrador confirma por outro canal que foi a pessoa quem pediu e que ela é maior de 18 anos (regra do MVP).
2. O administrador envia `/aprovar <id>`.
3. O sistema muda o status de PENDENTE para ATIVO.
4. O sistema confirma ao administrador.
5. O sistema avisa o testador que o acesso foi liberado.

**Fluxos alternativos**
- 2a. Quem envia não é o administrador: o comando não é reconhecido.
- 2b. O identificador não existe ou é inválido: o sistema responde com mensagem própria.
- 3a. A pessoa já está ATIVA: o sistema avisa.
- 3b. A pessoa está BLOQUEADA: o sistema orienta a desbloquear pelo banco. Bloquear e desbloquear são feitos direto no banco, sem story.

**Pós-condição:** o testador está ATIVO e pode usar UC-01, 03, 04 e 05.

---

## UC-08 (rebaixado): tratar mensagem fora do assunto

Não é caso de uso próprio. Não há objetivo de ator que comece nisso; é um desvio que acontece dentro do UC-01 (1a e 6f). Fica como regra transversal. *(Decidido em 01/11/2026.)*

**RT-01 Mensagens fora do assunto** (US-10)
- Xingamento: resposta curta e neutra, sem rebater. Nada é registrado como treino.
- Dor ou lesão: o bot anota sem diagnosticar e sugere procurar um profissional de saúde. Não recomenda o que fazer com a carga.
- Pedido de dieta, medicação ou diagnóstico: o bot recusa e indica um profissional.
- Assunto sensível ou emergência: o bot não aconselha e responde com um texto **fixo**, escrito de antemão, sem geração livre pelo modelo. Texto aprovado pelo autor em 01/11/2026 (conferir os números antes do teste):

  > "Sinto muito que você esteja passando por isso. Eu só registro treino e não consigo ajudar nisso, mas você merece apoio de quem pode. Se for uma emergência de saúde, ligue 192 (SAMU). Se estiver sofrendo muito emocionalmente, ligue 188 (CVV, gratuito, 24 horas) ou procure agora alguém de confiança."

- Verificação: lista de mensagens de teste desses casos, rodada como regressão.

---

## UC-09 Excluir dados a pedido

- **Ator primário:** Administrador. **Interessado:** Testador (quem pede).
- **Pré-condição:** o testador pediu a exclusão. O pedido chega por mensagem ao administrador, pelo canal de fora do bot.
- **Gatilho:** o administrador recebe o pedido.

**Fluxo principal**
1. O testador pede a exclusão dos dados.
2. O administrador confirma que o pedido é do testador.
3. O administrador apaga o **histórico** do testador (mensagens, sessões e séries, inclusive as desfeitas). Cadastro, ficha e apelidos ficam. Se o testador pediu para **sair**, apaga também cadastro, ficha e apelidos.
4. O administrador descarta backups com mais de 30 dias e, se restaurar um backup, refaz as exclusões do registro privado.
5. O administrador confirma ao testador que foi feito.

**Fluxos alternativos**
- 2a. Não é possível confirmar quem pediu: o pedido não é atendido.
- 3a. Exclusão total: o testador só volta com novo `/start` e nova aprovação.

**Pós-condição:** dentro do prazo de 7 dias corridos, nenhum histórico do testador permanece no banco (mensagens, sessões e séries); na exclusão total, também não permanecem cadastro, ficha e apelidos.

*(Fechado em 07/11/2026: D2 do `06-modelo-de-dados.md`.)*

---

## UC-10 Cadastrar a ficha do testador

- **Ator primário:** Administrador.
- **Pré-condição:** o testador está ATIVO e enviou a ficha de treino dele.
- **Gatilho:** o administrador recebe a ficha.

**Fluxo principal**
1. O administrador confere os nomes dos exercícios, o grupamento e a forma da carga (por lado ou total) de cada um.
2. O administrador cadastra os exercícios do testador no catálogo e os apelidos dele.
3. O administrador confere a contagem final de exercícios e apelidos.

**Fluxos alternativos**
- 1a. Um nome ou apelido serve para mais de um exercício: não vira apelido; fica fora até o testador esclarecer.
- 2a. Exercício já existe no catálogo: o administrador reutiliza em vez de duplicar.

**Pós-condição:** o catálogo do testador tem os exercícios e apelidos da ficha dele.

**Fronteira (decidido em 01/11/2026):** a decisão 4 de 28/10 dizia que a ficha é carregada por script "fora da fronteira do sistema" e, ao mesmo tempo, que entra como caso de uso. Ficou resolvido assim: o banco está dentro da fronteira e o script é a interface do administrador. Por isso UC-09 e UC-10 são casos de uso, e o modelo de dados precisa suportar cadastrar e apagar dados por testador. A decisão 4 deve ser reescrita nesse sentido ao ganhar página em `decisoes/`.

---

## Cobertura das stories

| Story | Onde está |
|---|---|
| US-01, 02, 03, 17, 18 | UC-01 (US-02 também no UC-02) |
| US-04 | UC-04 |
| US-06 | UC-03 |
| US-07, 08 | UC-06, UC-07 |
| US-09 (tom) | Não é UC: é qualidade transversal. Os critérios valem para todas as respostas do bot (ver RT-01). |
| US-10 | RT-01 |
| US-11 | UC-06 (passo 3) |
| US-12 | UC-09 |
| US-19 | UC-05 |
| RNF-01 a 05 | Transversais; RNF-01 e 02 aparecem no UC-01 |
| (nenhuma) | UC-10 cobre a lacuna da ficha, sem story própria |

## Decisões do autor em 01/11/2026

1. **Decisão 2 aceita (proposta A):** a série ambígua ou desconhecida é gravada na hora, sem exercício, e a confirmação traz botões. Não há espera de resposta nem estado EXPIRADO.
2. **Apelido:** o vínculo cria apelido só quando o texto não casou com nenhum exercício; texto ambíguo nunca vira apelido.
3. **UC-10 mantido como caso de uso;** a fronteira do sistema inclui o banco.
4. **UC-08 vira regra (RT-01).**
5. **Evolução (UC-05):** sem filtro de período, mínimo de 3 registros (ponto de partida, a revisar após a 1ª semana de teste).
6. **Excluir cadastro, prazo e backup (UC-09):** adiados.
7. **Texto de emergência (RT-01):** aprovado. Deve ser texto fixo, nunca gerado pelo modelo.
8. **Desfazer (UC-03):** sempre da mensagem inteira e sem confirmação no MVP. Desfazer parcial e Refazer ficam para depois do teste.
9. **Comando de evolução:** `/evolucao` (sem acento, limitação do Telegram).

## Ainda em aberto

Nada em aberto no modelo de dados (ver `06-modelo-de-dados.md`).

## Consequências nos outros documentos

- `04-ciclo-de-vida-do-registro.md`: atualizado em 01/11/2026 pela decisão 2 (saem AGUARDANDO_RESPOSTA e EXPIRADO; a pendência de vínculo vira atributo da série).
- `decisoes/`: as decisões 2 a 5 de 28/10 ainda não têm página. A 2 e a 4 mudam de texto com o que foi decidido hoje.
- `00-roteiro.md`: "Casos de uso" marcado como feito em 01/11/2026.
