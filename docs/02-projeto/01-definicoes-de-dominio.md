# Definições de domínio

Fase 2 (Projeto). Rascunho de 27/10/2026, a partir das respostas do autor aos glossários (`01-glossario-de-dominio.md` e o glossário de vocabulário avançado). Documento vivo.

Este documento diz o que cada palavra significa **no Atlas**. O glossário diz como as pessoas falam; aqui se decide o que o sistema faz com isso. Itens marcados [decidir] seguem abertos.

---

## Sessão
**Definição:** um bloco contínuo de treino realizado, com data. É o que aconteceu, não o que estava planejado.

- Uma pessoa pode ter **mais de uma sessão no mesmo dia** (ex.: preparação de bodybuilder, treino de manhã e à noite). Isso substitui a regra do repositório antigo, "uma sessão por usuário por data".
- A sessão pertence a um usuário e guarda a observação do dia (contexto: academia lotada, tempo, alimentação).
- **No MVP só entra musculação.** Cardio e corrida vão como texto na observação da sessão (decisão anterior mantida). O modelo deve deixar espaço para uma modalidade no futuro, sem implementá-la agora.
- [decidir] **Como o bot sabe que começou uma segunda sessão no mesmo dia?** Proposta: por padrão, a mensagem entra na sessão do dia; só abre outra sessão quando o usuário disser explicitamente ("segundo treino", "treino da noite"). O bot não deve deduzir por intervalo de horário, porque isso seria inferência. A confirmação mostra em qual sessão a série entrou.

## Rótulo do treino (treino A, B, C)
**Definição:** nome da ficha que o usuário seguiu na sessão ("treino A", "treino B", "treino de peito"). É uma etiqueta de texto opcional da sessão.

- Protege as duas leituras de "treino": a **sessão** é o realizado, e o **rótulo** é só o nome que o usuário dá a ela.
- O sistema **não** deduz exercícios a partir da letra, porque as letras mudam de pessoa para pessoa (A pode ser peito, pode ser empurrar).
- A **ficha** (o plano prescrito) fica fora do MVP. Quando entrar, o rótulo poderá apontar para ela, e o modelo de hoje não precisa mudar.

## Série
**Definição:** grupo de repetições seguidas de um exercício, sem descanso no meio. Pertence a uma sessão e a um exercício.

- **"3x10" é o realizado.** O bot trata como 3 séries com 10 repetições cada, a menos que o usuário diga o contrário, e a confirmação mostra o que ele entendeu.
- Uma série é uma entrada, mesmo quando vem num atalho ("3x10" gera 3 séries).
- [decidir] **Atenção a "3x10" sem unidade:** com "kg" é carga ("3x20kg": 3 séries com 20 kg, repetições não ditas); sem unidade, repetições. O prompt v4 precisa de regra e de caso na regressão.

### Campos da série
| Campo | Obrigatório? | Observação |
|---|---|---|
| Exercício relatado | Sim | Como o usuário disse |
| Exercício oficial | Não | Vazio até casar com o catálogo; nunca por aproximação |
| Número da série | Sim | Por exercício, dentro da sessão |
| Tipo | Sim | `TRABALHO` (padrão), `AQUECIMENTO`, `PREPARATORIA` |
| Carga | Não | Vazio = não dito (nunca 0); aproximada? |
| Repetições | Não | Vazio = não dito (nunca 0); aproximadas? |
| Falha | Não | Sim, não ou vazio; só marcada quando dita |
| Texto livre | Não | Ver abaixo |
| Etapas (drop set) | Não | Série com várias etapas, cada uma com carga e repetições |

- Forma da carga (por lado ou total) fica no exercício. [decidir no tópico 2: carga e equipamento]

### Tipo da série
- `TRABALHO`: a série da carga-alvo do dia. É o padrão quando o usuário não diz outra coisa.
- `AQUECIMENTO`: aquecimento específico, quando o usuário disser a palavra.
- `PREPARATORIA`: inclui o que o usuário chamar de **aproximação** ou **reconhecimento**, que ficam como sinônimos de preparatória.
- O bot só marca tipo quando o usuário usa a palavra. Nunca pela carga, porque nenhuma fonte define a fronteira entre eles.
- Aquecimento **geral** (esteira, mobilidade) não é série: vai para a observação da sessão.

### Falha
- Só é marcada quando o usuário diz ("falhei", "travou", "até a falha").
- **Nunca** converte em RIR 0 nem em qualquer outro número.
- A literatura mostra que "falha" tem vários sentidos, então o bot registra o que foi dito, sem classificar o tipo de falha.

### Texto livre da série
**Definição:** campo opcional em que fica, exatamente como foi escrito, o que o usuário disse sobre a série e o sistema não estrutura (RIR, RPE, "roubei", cadência, "dava mais uma").

- Entra no MVP.
- O sistema **não interpreta nem deduz** a partir dele. "RIR 2" ali **não** autoriza marcar `falha = não`.
- Estrutura só o que tiver definição estável e valor explícito, e isso fica para versão futura: depois da primeira semana de teste, olha-se quais termos apareceram de fato.
- Cuidado: esse campo pode receber dado de saúde ("ombro doendo"). Está no mesmo grupo de risco do texto bruto (LGPD) e segue o mesmo tratamento.

---

## Pontos abertos desta rodada
1. Como a segunda sessão do dia é aberta (proposta acima).
2. Regra para "3x10" com e sem unidade.
3. Forma da carga: por lado × total (tópico 2).
4. Ciclo de vida do registro: estados da mensagem (tópico 3).

## Decisões que precisam de página própria em `decisoes/`
- **Sessão deixa de ser uma por usuário por dia.** Contradiz a regra do repositório antigo e muda o modelo de dados.
- **Texto livre por série** entra no MVP.
