# Definições de domínio

Fase 2 (Projeto). Atualizado em 27/10/2026, a partir das respostas do autor aos glossários (`01-glossario-de-dominio.md`, `02-glossario-vocabulario-avancado.md` e `03-glossario-catalogo-e-carga.md`). Documento vivo.

Este documento diz o que cada palavra significa **no Atlas**. Os glossários dizem como as pessoas falam; aqui se decide o que o sistema faz com isso. Itens marcados [decidir] seguem abertos.

---

## Sessão
**Definição:** um bloco contínuo de treino realizado, com data. É o que aconteceu, não o que estava planejado.

- **Uma sessão por dia por padrão.** A mensagem entra na sessão do dia. O bot só abre outra sessão quando o usuário disser explicitamente ("segundo treino", "treino da noite"). Cobre quem treina duas vezes no dia. Decisão em `decisoes/2026-10-27-sessao-pode-ser-mais-de-uma-por-dia.md`.
- O bot **não** abre sessão nova pelo horário nem pelo intervalo entre mensagens (seria inferência, contra o RNF-01). Se a série entrar na sessão errada, a confirmação mostra a sessão e o `/desfazer` corrige.
- A sessão pertence a um usuário e guarda a observação do dia (contexto: academia lotada, tempo, alimentação).
- **No MVP só entra musculação.** Cardio e corrida vão como texto na observação da sessão. O modelo deixa espaço para uma modalidade no futuro, sem implementá-la agora.

## Rótulo do treino (treino A, B, C)
**Definição:** nome da ficha que o usuário seguiu na sessão ("treino A", "treino B", "treino de peito"). É uma etiqueta de texto opcional da sessão.

- Protege as duas leituras de "treino": a **sessão** é o realizado, e o **rótulo** é só o nome que o usuário dá a ela.
- O sistema **não** deduz exercícios a partir da letra, porque as letras mudam de pessoa para pessoa.
- A **ficha** (o plano prescrito) fica fora do MVP. Quando entrar, o rótulo poderá apontar para ela.

---

## Exercício e catálogo
**Definição:** o exercício oficial é uma variação concreta (ex.: "Supino inclinado halter", "Supino inclinado Smith"). Cada variação é um exercício separado, porque a carga e a forma de registrar não são comparáveis entre equipamentos.

- **Atributos** (metadados, não identidade): movimento-base, equipamento, pegada, ângulo, unilateral ou bilateral. Servem para agrupar e para montar a pergunta quando o nome vem incompleto.
- **Grupo de variação:** vínculo opcional entre exercícios da mesma família, para comparar sem misturar histórico.
- **Tipo de registro** (por exercício): `PESO_E_REPS`, `CORPORAL` (só repetições), `CORPORAL_COM_LASTRO`, `ASSISTIDO`, `DURACAO`. Evita ler "assistida 20" como carga. Barra fixa sem peso é carga 0 quando dito; vazio continua sendo "não dito".
- **Forma da carga padrão** (por exercício): `POR_LADO` ou `TOTAL`. Ver a regra de carga abaixo.
- **Faixa-alvo de repetições:** opcional (as duas pontas ou nenhuma).
- **No MVP, o catálogo de cada testador vem da ficha dele**, com cerca de 50 exercícios. Não se importa base externa (wger e similares ficam fora do MVP por causa das licenças).

### Apelidos
- Pertencem ao **usuário**, são guardados em minúsculas, e cada um aponta para **um único exercício**.
- Nomes ambíguos não viram apelido global ("remada", "puxada", "pulley", "scott", "cavalinho", "crucifixo"). O bot **lista as candidatas da ficha do próprio usuário** e pergunta, em vez de assumir.
- Só assume quando houver um único candidato na ficha do usuário, e a confirmação mostra o que ele entendeu.

### Exercício fora do catálogo
- Fica registrado como **não catalogado**, com o texto original e as séries já interpretadas, **pendente de vínculo**, sem prazo de expiração no MVP.
- Quando o usuário confirmar, a série é religada ao exercício (novo ou existente). Nunca se cria exercício oficial automaticamente.

---

## Série
**Definição:** grupo de repetições seguidas de um exercício, sem descanso no meio. Pertence a uma sessão e a um exercício.

- Uma série é uma entrada, mesmo quando vem num atalho ("3x10" gera 3 séries).

### Leitura de "NxM" (decidido em 27/10/2026)
- **O bot assume a notação convencional:** "3x10" é 3 séries de 10 repetições, e é o que foi realizado. A confirmação mostra "3 séries de 10 repetições", e o `/desfazer` corrige se estiver errado.
- **Com unidade, é carga:** "3x20kg" é 3 séries com 20 kg, repetições não ditas (vazio, nunca 0).
- **Quando há outro "NxM" na mesma frase** (ex.: "halter 2x20, 3x10"), o bot confirma antes de tratar o primeiro como "2 halteres de 20".
- **O bot só pergunta quando não há como resolver:** por exemplo, "3x10" sem exercício e sem exercício recente claro (depende da janela de contexto da US-02, de 30 minutos).
- O prompt v4 precisa de regra e de casos de regressão para isso.

### Carga (decidido em 27/10/2026)
A forma da carga **padrão fica no exercício**, e a série guarda o que foi dito:

| Campo da série | Observação |
|---|---|
| Texto original da carga | Prova do que o usuário escreveu |
| Número informado | Vazio = não dito (nunca 0); aproximado? |
| Forma declarada | por lado, total ou não declarada |
| Carga normalizada | Na forma padrão do exercício; vazia se não der para calcular |
| Origem | explícita, derivada (ex.: 40 total → 20 por lado) ou padrão do exercício |

Regras:
- **Halter, kettlebell e implementos únicos:** o número é o peso de **um** implemento. "10 cada" é 10 por halter. Isso vale também para afundo e búlgaro com dois halteres. Quando o exercício é unilateral, o registro continua unilateral.
- **"20 cada lado", "20 cada", "20 de cada":** explícito, por lado ou por mão.
- **"40 no total":** explícito, total. Em exercício `POR_LADO` com implemento simétrico, deriva 20 por lado, com origem "derivada".
- **Barra, Smith e leg press:** o bot registra as **anilhas por lado** e só calcula o total quando o peso da barra ou da plataforma for conhecido. Quem fala "a barra pesa 15" e quem fala "20" convivem, e por isso o sistema não assume.
- **Polia e máquinas de pilha:** o número da pilha fica como está. Só é comparável dentro do mesmo aparelho.
- **Placa e pino ("placa 5", "pino 8"):** unidade própria, **sem conversão para kg**.
- **kg e lb:** converte para kg guardando o original. Aceita vírgula decimal ("2,5").
- **Local ou equipamento (academia) na série:** fora do MVP. Se o usuário citar a academia, vai no texto livre.
- **Peso corporal e assistência:** a carga é só o acréscimo (lastro). Assistência é tipo de registro próprio (`ASSISTIDO`), nunca carga negativa.

### Campos da série
| Campo | Obrigatório? | Observação |
|---|---|---|
| Exercício relatado | Sim | Como o usuário disse |
| Exercício oficial | Não | Vazio até casar com o catálogo; nunca por aproximação |
| Número da série | Sim | Por exercício, dentro da sessão |
| Tipo | Sim | `TRABALHO` (padrão), `AQUECIMENTO`, `PREPARATORIA` |
| Carga | Não | Os cinco campos da tabela acima |
| Repetições | Não | Vazio = não dito (nunca 0); aproximadas? |
| Falha | Não | Sim, não ou vazio; só marcada quando dita |
| Texto livre | Não | Ver abaixo |
| Etapas (drop set) | Não | Série com várias etapas, cada uma com carga e repetições |

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
**Definição:** campo opcional em que fica, exatamente como foi escrito, o que o usuário disse sobre a série e o sistema não estrutura (RIR, RPE, "roubei", cadência, "senti que sairia mais uma"). Decisão em `decisoes/2026-10-27-texto-livre-por-serie.md`.

- Entra no MVP.
- O sistema **não interpreta nem deduz** a partir dele. "RIR 2" ali **não** autoriza marcar `falha = não`.
- **Série × dia:** o que se refere à série vai para o texto livre da série; o que se refere ao dia ("hoje estou cansado") vai para a observação da sessão.
- Estrutura só o que tiver definição estável e valor explícito, e isso fica para versão futura, depois da primeira semana de teste.
- **Dado de saúde:** esse campo pode receber relato de dor ("ombro doendo"). Está no mesmo grupo de risco do texto bruto (LGPD) e segue o mesmo tratamento (aviso na boas-vindas, exclusão a pedido). O bot responde como na US-10: anota sem diagnosticar e sugere procurar um profissional de saúde. Ele **não** recomenda o que fazer com a carga.

---

## Pontos abertos
1. Validar a janela de 30 minutos da US-02 depois da primeira semana de teste.
2. Prazo e método da exclusão de dados (US-12).
3. Página em `decisoes/` para as decisões de catálogo e carga (opcional).
