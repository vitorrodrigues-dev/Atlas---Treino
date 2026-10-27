# Catálogo de exercícios, equipamento e registro de carga para um chatbot de treino em PT-BR

Resposta direta: mantenha a forma da carga (POR_LADO/TOTAL) fixada no exercício como convenção padrão, mas grave em cada série o texto original, a forma efetivamente declarada e se o valor foi explícito ou inferido. Também vale manter as variações como exercícios oficiais separados, acrescentando atributos estruturados (movimento-base, equipamento, pegada, ângulo, unilateral) só como metadados. E nunca converta "2x20", "placa 5" ou "meia anilha" em quilos sem confirmação.

## TL;DR

- **(A) Forma da carga:** a decisão "fixa no exercício" está certa como padrão, e é o que os apps documentam: Fitbod mostra o peso de um halter e calcula o volume como se fossem dois, e Strong tem a opção "Count Dumbbells Twice". Só que ela é insuficiente para um chatbot de texto livre. Cada série precisa guardar a forma declarada ("40 no total") e a origem do valor (explícito, inferido ou padrão do exercício). Além disso, nem POR_LADO nem TOTAL resolvem a comparação entre academias: o peso da barra Smith varia de 4 a 20 kg, e uma polia com razão 4:1 entrega um quarto da pilha.
- **(B) Catálogo:** exercícios oficiais separados por variação é a escolha certa para o MVP (≈ 50 por testador). Hevy, por exemplo, amarra a cada exercício um único equipamento e um "tipo de exercício" que não pode ser editado depois de salvo. Acrescente atributos estruturados e um "grupo de variação", como faz o wger, para permitir comparações e sugestões sem mudar a identidade do exercício.
- **(C) Interpretação:** "20 cada lado", "20 cada" e "20 de cada" são explícitos (por lado/por mão). "40 no total" é explícito (total). "2x20" é ambíguo, porque no Brasil "3x10" significa séries × repetições. Medidas sem quilo ("placa 5", "anilha de 10" numa máquina, "meia anilha") devem ser guardadas como texto com unidade própria, sem conversão.

## Sumário

1. Decisão A: onde fica a forma da carga
2. Decisão B: exercício separado ou exercício-base com atributos
3. Decisão C: como interpretar "20 cada lado", "2x20", "40 no total"
4. Nomes e variações no PT-BR
5. Bases de exercícios abertas e licenças
6. Carga e equipamento por tipo
7. Problemas típicos de chatbot
8. Tabela-resumo
9. Classificação e limites das fontes
10. Recomendações práticas
11. Decisões que o autor precisa tomar

---

## 1. Decisão A: forma da carga no exercício ou na série?

**Posição:** fica no exercício como *padrão*, e a série guarda *o que foi dito*. Não é uma escolha entre um e outro: são dois campos com funções diferentes.

**O que os apps fazem (documentação oficial):**
- **Fitbod:** o peso exibido é por halter, e o app calcula o volume como se os dois halteres fossem usados (exemplo da própria ajuda: 25 lb por halter × 10 reps = 500 lb de volume). Para barra, o app oferece duas convenções, "Bar + Plates" (barra mais anilhas) e "Plates Only" (só anilhas). Em máquina unilateral, a orientação é registrar o peso de um lado, sem que o app dobre.\[1\]
- **Strong:** no iOS existe a opção "Count Dumbbells Twice", que decide se o volume de exercícios com halter é dobrado.\[2\] No Android o peso de todos os exercícios com halter é dobrado automaticamente.\[3\] Ou seja, o mesmo app tem comportamentos diferentes por plataforma.
- **Hevy:** na configuração do Hevy Trainer, os pesos de halter cadastrados representam *um* halter, mas a sugestão para exercícios com dois halteres é exibida como soma (halteres de 5–30 lb geram sugestões de 10–60 lb).\[4\] Isso mostra que nem dentro de um mesmo app a convenção é uniforme entre telas.
- **StrengthLog:** a regra depende do músculo que recebe a carga. No afundo com dois halteres de 10 kg registra-se 20 kg, porque cada perna carrega os dois. No supino com halteres registra-se 10 kg.\[5\]
- **JEFIT:** não achei regra oficial. Os fóruns da própria comunidade mostram usuários divididos entre "peso de um halter" e "soma", e um usuário pede justamente que o app distinga, por exercício, se o peso é total ou individual.\[6\]\[7\]\[8\]
- **Stronger by the Day:** a ajuda diz que qualquer forma serve, desde que seja consistente, e sugere anotar a convenção nas notas.\[2\]\[9\]

**Prós de fixar no exercício (decisão atual):**
- Comparação histórica do mesmo exercício fica trivial: todas as séries estão na mesma base.
- Combina com o que os apps maduros fazem: convenção por equipamento ou exercício, com o volume ajustado internamente.
- Simplifica o parser: um "20" isolado ganha sentido pelo exercício.

**Contras e riscos:**
- O usuário às vezes declara a outra forma ("40 no total" num exercício POR_LADO). Se a série não guardar isso, a conversão fica invisível e não pode ser auditada.
- Exercícios em que a convenção é discutível (afundo/búlgaro com halteres, segundo o StrengthLog; máquinas articuladas com braços independentes) podem ter a forma escolhida "errada" para aquele usuário.
- **Comparação entre academias e equipamentos:** POR_LADO x TOTAL é só um dos problemas, e não o maior. Um "supino Smith 40 kg" em duas academias pode diferir pelo peso efetivo da barra (4–20 kg, conforme fabricante e contrapeso). Um "crossover 20" pode diferir em 2× ou 4× conforme a razão da polia. Nenhuma das duas opções torna esses números comparáveis. O que torna comparável é registrar *o equipamento/academia* e não misturar históricos de equipamentos diferentes sem aviso.

**Recomendação concreta para a série (sem código):** guarde (1) o texto original, (2) o número informado, (3) a forma declarada (por lado / total / não declarada), (4) a carga normalizada na forma do exercício, e (5) a origem: *explícita*, *derivada* (ex.: 40 total → 20 por lado) ou *padrão do exercício*. Opcionalmente, (6) o local/equipamento. Com isso a decisão "fixa no exercício" continua valendo, mas sem perder informação.

*Frequência no relato (estimativa, sem fonte):* "20" sem qualificador deve ser a maioria dos relatos com halter, e "cada"/"cada lado" aparece numa minoria relevante. "No total" é raro. *Só texto livre é seguro?* Não para o número principal (é ele que gera progressão). Sim para guardar o original como prova.

## 2. Decisão B: exercício oficial separado ou exercício-base com atributos?

**Posição:** mantenha exercícios oficiais separados (decisão atual) e acrescente atributos como metadados, mais um vínculo de "família/grupo de variação". A identidade do exercício continua sendo a variação concreta.

**O que se sabe publicamente sobre os apps:**
- **Hevy:** a biblioteca tem 400+ exercícios filtráveis por equipamento e músculo. O exercício personalizado tem nome, *um* equipamento, músculo primário, secundários e um *tipo de exercício*: peso & reps, reps com peso corporal, peso corporal com carga, peso corporal assistido, duração, duração & peso, distância & duração, peso & distância. A central de ajuda oficial do Hevy avisa que, depois de salvo, o tipo do exercício personalizado não pode ser editado: para mudar, é preciso duplicar ou recriar, e o histórico zera. Segundo a mesma ajuda, uma conta gratuita pode criar até 7 exercícios personalizados, e a assinatura Pro libera exercícios ilimitados. Na prática, cada combinação movimento + equipamento é um exercício próprio, com atributos fixos.
- **Fitbod:** a documentação pública trata o equipamento e a lateralidade ("per arm", "per leg") como propriedades do exercício que mudam a leitura de peso e reps.\[10\] Não encontrei documentação pública sobre um modelo de "exercício-base + atributos".
- **Strong e JEFIT:** não encontrei documentação pública sobre o modelo de dados do catálogo. Não afirmo como funcionam.
- **wger (base aberta):** cada exercício é um registro próprio (categoria, músculos, equipamento), e os nomes ficam em traduções separadas do registro-base.\[11\] Existe um campo `variations` que liga exercícios do mesmo grupo de variação.\[12\]\[13\] É o modelo híbrido que recomendo.

**Prós de exercícios separados:**
- O histórico e a progressão de "Supino inclinado halter" não se misturam com os de "Supino inclinado Smith", e de fato não deveriam: carga e convenção diferem.
- A forma da carga (decisão A) e o tipo de registro (peso, assistido etc.) ficam bem definidos por exercício, como no Hevy.
- Com ≈ 50 exercícios por testador, o custo de manutenção é baixo.

**Contras:**
- Explosão combinatória se o catálogo crescer (pegada × ângulo × equipamento × unilateral).
- Comparações entre variações ("quanto evoluí em supino inclinado, qualquer equipamento?") exigem um vínculo extra.
- Relatos parciais ("supino inclinado 20") casam com vários exercícios. É aí que os atributos ajudam a montar a pergunta ("halter, Smith ou máquina?").

**Contras de exercício-base com atributos como identidade:**
- O histórico mistura equipamentos com cargas incomparáveis, a menos que todo gráfico filtre por atributo.
- A forma da carga deixa de ser fixa: passa a depender do atributo equipamento, o que cria mais regras.
- Atributos faltando no relato viram nulos difíceis de tratar.

**Ponto de atenção na literatura brasileira:** Nuzzo (Journal of Strength and Conditioning Research, 2021, "Inconsistent Use of Resistance Exercise Names in Research Articles") não propõe um "nome oficial único", e sim um sistema com um padrão de nomeação e diretrizes para comunicar os nomes dos exercícios. Outro trabalho sobre padronização sugere o padrão especificação + equipamento + exercício. As duas propostas casam com o modelo "nome oficial + atributos". Um estudo da Revista Brasileira de Cineantropometria e Desempenho Humano ouviu 191 estudantes e profissionais de Educação Física do Rio de Janeiro entre agosto de 2013 e maio de 2014 e não encontrou associação entre o nome escolhido e o grau acadêmico. Com os mesmos 191 participantes (130 homens e 61 mulheres), um trabalho da Revista de Atenção à Saúde/USCS encontrou diferenças significativas entre as opções de nome para nove dos dez exercícios. Os autores leem isso como evidência de que existe um nome predominante para nove dos dez, não de padronização total. Um estudo de fichas de academias do interior paulista (Revista Educação Física UNIFAFIBE, 2018) concluiu que não há padronização nas fichas.\[14\]

## 3. Decisão C: como interpretar sem inventar

**Princípio:** só converta quando a expressão é inequívoca *e* o equipamento é conhecido. Fora disso, guarde o texto e pergunte, ou registre como "não declarado" usando o padrão do exercício, marcando a origem.

| Expressão | Leitura segura | Quando perguntar | Guardar só como texto? |
|---|---|---|---|
| "20 cada lado" (halter) | 20 por mão, explícito | Nunca | Não |
| "20 cada lado" (barra/Smith/máquina de anilha) | 20 *de anilhas* por lado; a barra/plataforma **não** está incluída | Se o exercício é TOTAL e o peso da barra/plataforma é desconhecido: perguntar ou guardar sem total | Guardar o total só se o peso da barra for conhecido |
| "20 cada" / "20 de cada" | Igual a "cada lado" (por mão ou por lado) | Se o exercício usa um único implemento (ex.: halter único no tríceps francês) | Não |
| "40 no total" | Total explícito; em exercício POR_LADO com implemento simétrico, derivar 20 por lado marcado "derivada" | Se não há simetria (pilha única, cabo único) | Não |
| "2x20" | **Ambíguo**: no Brasil "NxM" costuma ser séries × reps | Sempre, salvo quando outro "NxM" na mesma frase já ocupa séries × reps (ex.: "halter 2x20, 3x10" → provável "2 halteres de 20", mas confirme na 1ª vez) | Sim, até confirmar |
| "20" sozinho | Valor na forma padrão do exercício, origem "padrão" | Se o exercício ainda não foi identificado | Não |
| "placa 5", "anilha de 10" na máquina, "pino no 8" | Posição/unidade da máquina, **não** kg | Não precisa perguntar; registrar como unidade "placa/posição" | Sim (com unidade própria) |
| "meia anilha", "anilha pequena" | Indefinido (depende da academia) | Perguntar kg só se o usuário quiser precisão | Sim |
| "45 lb", "libras" | Converter para kg guardando o original | Se a unidade não está escrita e o valor é atípico | Não |

*Frequência no relato (estimativa, sem fonte):* "NxM" para séries × reps aparece em praticamente toda mensagem. "2x20" no sentido de carga é raro, e por isso a leitura padrão deve ser séries × reps. "cada"/"cada lado" é frequente com halter e anilhas em leg press/Smith. "placa"/"pino" deve ser comum em máquinas de pilha.

---

## 4. Nomes e variações no PT-BR

**Estrutura típica do nome.** Na prática de academia o nome segue *movimento + (posição/ângulo) + equipamento + (pegada/lateralidade)*: "supino inclinado com halter", "puxada frontal pegada supinada", "remada unilateral (serrote)". Um material do CREF-SP propõe nomear pelo movimento articular principal, com o nome popular entre traços ("adução horizontal de ombros – supino máquina"), e reconhece que no dia a dia se usam os nomes populares.\[15\] Para o chatbot, isso significa que o nome popular é o que o usuário digita, e o anatômico serve no máximo como metadado.

**Português x inglês x aportuguesado.** Coexistem termos em inglês usados como nome próprio ("leg press", "hack machine", "smith", "crossover/cross over", "pulley", "stiff", "peck deck"), traduções ("prensa de pernas", "barra guiada", "puxador") e grafias variadas ("peck deck" x "pec deck", "cross over" x "crossover", "pull down" x "pulldown").\[16\]\[17\]\[18\] Os glossários PT-BR de academia e de inglês (Open English, KNN, Inglês Online, Central do Inglês, Nextfit, Suplemento PE) confirmam essas equivalências, mas são fontes comerciais/educacionais não científicas. "Lag" não apareceu em nenhuma fonte que consultei. Não confirmo o uso nem o sentido.

**Apelidos e gírias (com divergências):**
- **Voador = peck deck.** É consistente entre a Wikipédia PT, glossários e blogs.\[19\]\[20\] Divergência: o blog da Nextfit põe "crucifixo" como sinônimo do peck deck, enquanto outros listam "crucifixo (máquina ou halter)" como exercício próprio.\[16\]\[21\] Logo, "crucifixo" sozinho é ambíguo (halter, máquina ou polia).
- **Pulley.** Designa ao mesmo tempo o *aparelho* (polia) e, por elipse, o *exercício* "pulley frente" (= puxada frontal, puxada alta, puxador frente).\[22\]\[23\]\[24\] Existe ainda "pulley costas/puxada atrás".\[16\] "Pulley" sozinho é ambíguo, o que confirma a decisão do projeto.
- **Cavalinho:** é o caso mais divergente encontrado. Para Uniguaçu, Treino Mestre e o blog Braço Forte, "remada cavalinho" é a remada na barra T / ponta de barra / "canto de parede".\[25\]\[26\]\[27\] Para a Academia Central Fitness, é sinônimo de remada curvada ("bent-over row").\[28\] A Suplemento PE lista "remada baixa (cavalinho ou máquina)".\[16\] A Evox Fitness diz que pode ser feita em estação com cabo.\[29\] "Cavalinho" não deve virar apelido global: deve ser apelido *do usuário* e confirmado na primeira vez.
- **Tríceps testa x tríceps francês:** a Integralmedica e a Treino Mestre diferenciam pela posição. O testa é feito deitado, com o ombro a cerca de 90°; o francês, sentado ou em pé, com o braço acima da cabeça.\[30\]\[31\] A hipertrofia.org chama ambos de "rosca" ("rosca testa", "rosca francesa").\[32\] Há divergência sobre qual enfatiza a cabeça longa: a hipertrofia.org diz que é o francês, e uma citação na TotalPass diz o contrário.\[32\]\[33\] Para o catálogo isso não importa, mas mostra que esses textos não são fontes técnicas confiáveis.
- **Scott:** designa o banco ("banco Scott"),\[20\] e "rosca scott" pode ser feita com barra, halter ou máquina. A decisão do projeto de não aceitar "scott" como apelido está correta.

**Mesmo nome, movimentos diferentes:** "remada" (curvada, baixa, cavalinho, serrote, alta, que é de ombro/trapézio), "puxada" (frente, atrás, alta, articulada), "crucifixo" (halter, máquina, polia, inverso), "cavalinho" (barra T x curvada), "pulley" (aparelho x exercício).

**Nomes parecidos, exercícios diferentes:** "remada alta" (trapézio/ombro) x "puxada alta" (dorsal); "tríceps testa" x "tríceps francês"; "cadeira flexora" x "mesa flexora"; "stiff" x "levantamento terra"; "leg press 45°" x "leg press horizontal".

*Frequência no relato (estimativa):* apelidos e nomes populares devem dominar, e nomes anatômicos devem ser raros. Termos ambíguos como "remada" e "puxada" devem aparecer com frequência sem complemento. *Seguro só como texto?* Não: o nome precisa ser resolvido para um exercício para haver histórico.

## 5. Bases de exercícios abertas e licenças

| Base | Tamanho | Estrutura | Português | Licença | Uso comercial / atribuição |
|---|---|---|---|---|---|
| **wger** | ~845+ exercícios (contagem de terceiros) | API REST; registro-base (categoria, músculos, equipamento) + traduções separadas; campo `variations` (grupo de variação); campos de licença e autor por registro | Idioma ID 7 (fontes de terceiros); nem todo exercício tem tradução. Em out/2026 um colaborador enviou 415 traduções pt-BR "assistidas por IA, amostradas por humanos"; o mantenedor publicou parte delas | Código AGPL-3.0+; **dados de exercícios sob Creative Commons por registro** (o README manda ver cada entrada); documentação CC-BY-SA-4.0.\[12\] Terceiros relatam mistura de CC-BY-SA 4, CC-BY-SA 3 e CC0 | Permite uso comercial, **mas** exige atribuição e, nos itens CC-BY-SA, compartilhamento pela mesma licença do material derivado. Guardar licença e autor de cada item importado\[12\]\[34\]\[35\]\[36\]\[37\] |
| **free-exercise-db** (yuhonas) | 800+ segundo o README do repositório (um catálogo de terceiros cita 873 JSONs) | JSON por exercício + JSON combinado, com schema; imagens no repositório | Não (inglês) | **Unlicense** (domínio público) | Permite uso comercial sem atribuição. Ressalva de um concorrente comercial (RepDB): a procedência das fotos é "menos clara" |
| **ExerciseDB** (nome usado por produtos diferentes) | exercisedb.dev: anuncia 5.000+;\[38\] ExerciseDB.io: 1.394 (pacote pago) | API hospedada / arquivos para download | Não verificado | exercisedb.dev: repositório com arquivo AGPL-3.0 mas sem código, e termos de uso próprios (licença revogável, só durante a assinatura, proíbe produto concorrente). ExerciseDB.io: compra única, licença perpétua, proíbe revenda e redistribuição | Comercial apenas sob contrato pago; não é "aberto" no sentido útil\[39\]\[40\]\[41\]\[42\]\[43\] |
| **MuscleWiki API** (comercial) | 1.900+ exercícios, 14 idiomas | API paga | Não verificado se inclui pt | Termos comerciais; exige manter a marca nos vídeos | Uso comercial nos planos pagos; proíbe redistribuir\[44\]\[45\] |

**Vale reaproveitar?**
- **Estrutura:** sim, do wger. O modelo "registro-base + traduções + grupo de variação + licença por item" é exatamente o híbrido recomendado na decisão B.
- **Nomes:** pouco valor. As bases são em inglês ou alemão, e as traduções pt-BR do wger são recentes e parcialmente geradas por IA. Os nomes populares brasileiros ("voador", "cavalinho", "pulley frente") não estão lá. Para o MVP, a ficha do testador é a melhor fonte de nomes.
- **Músculos/equipamento como seed:** o free-exercise-db é o mais livre juridicamente, por ser domínio público.

## 6. Carga e equipamento por tipo

- **Halter:** convenção dominante = peso de *um* halter (Fitbod; Hevy no cadastro de equipamento; StrengthLog para membros superiores). Exceção relevante: em membros inferiores com dois halteres, o StrengthLog registra a soma.\[4\]\[5\]\[10\] *Estimativa:* é o equipamento com mais "cada" no relato.
- **Barra (livre):** não há consenso sobre incluir a barra. O Fitbod oferece "Bar + Plates" e "Plates Only" como opções do usuário.\[46\] A barra olímpica de 20 kg é a referência citada nas fontes comerciais. Barras retas e W variam de peso, e não achei fonte confiável com valores padronizados. *Recomendação:* registrar o total incluindo a barra *quando conhecida*; quando o usuário relata "20 cada lado", guardar anilhas por lado e só computar o total se o peso da barra estiver cadastrado.
- **Anilhas em máquinas (leg press, hack):** o relato típico é por lado ("2 de 20 de cada lado"). O peso do carrinho/plataforma é desconhecido e varia por máquina. Não encontrei fonte com valores padronizados. Registrar anilhas, não "carga total".
- **Polia/cabo:** a placa da pilha **não é** a resistência no pegador quando há razão de polia. Life Fitness Signature Dual Adjustable Pulley: razão 4:1, pilha de 390 lb, resistência efetiva de 97,5 lb por pegador.\[47\] Matrix Aura G3-MSFT300/400: razão 1:4 (pilha de 131 kg → cerca de 32,9 kg por lado).\[48\] Life Fitness Signature Cable Crossover: 1:2 (fonte de catálogo, não do fabricante).\[49\] Marcas domésticas como Legend e Inspire usam 2:1.\[50\]\[51\] Não encontrei a razão de Technogym nem de Hoist. Conclusão: cargas de cabo são comparáveis só dentro do mesmo aparelho.
- **Máquinas de pilha/alavanca:** o Fitbod alerta que os números das pilhas variam por fabricante e nem sempre correspondem a um peso específico.\[10\] Um ensaio teórico da Revista de Educação Física (2016) mostrou que voadores com polia excêntrica e polia concêntrica impõem demandas de força diferentes para a mesma carga.\[52\] A relação entre "placa" e esforço não é linear nem comparável entre máquinas.
- **Smith:** segundo a MuscleSquad (fabricante), o peso efetivo da barra varia de 4 a 20 kg, com a maioria dos modelos entre 6 e 12 kg, e a barra do Multi-Functional Trainer da própria marca tem resistência-base de 7 kg. A Matrix Magnum declara resistência inicial de 11,3 kg.\[53\] Um fabricante brasileiro (Profitness) cita barras de 12 a 20 kg e um contrapeso que reduz a carga inicial.\[54\] As fontes divergem, e todas são comerciais.\[55\]\[56\]\[57\]\[58\] Registrar as anilhas e cadastrar o peso da barra por academia, se o usuário souber.
- **Peso corporal e lastro:** o Hevy separa peso corporal (reps), com carga (lastro somado) e assistido (o volume é peso corporal menos assistência, e só há recordes de reps). O Fitbod registra no campo de peso apenas a carga *adicional*.\[46\]\[59\]\[60\]\[61\] Para barra fixa, paralelas e flexão: carga 0 = só corpo; "+10" = lastro; "assistida 20" = contrapeso, que precisa de sinal ou tipo próprio para não ser lido como carga.
- **Elástico e kettlebell:** o kettlebell segue a regra do halter (peso de um implemento). Para elástico não encontrei convenção documentada: guardar cor/nível como texto.
- **Unidades:** kg x lb. Converter guardando o original. "Anilha de 10" em barra é kg; "placa 5" em máquina é posição. Fracionados (2,5 kg, 1,25 kg) devem ser aceitos com vírgula decimal ("2,5").

## 7. Problemas típicos de chatbot

- **Nome incompleto ou ambíguo:** com catálogo pequeno (≈ 50 por testador), a melhor estratégia é *listar candidatos da ficha do próprio usuário* ("Remada: baixa no triângulo, cavalinho ou serrote?"). É melhor do que perguntar em aberto e mais seguro do que assumir. Só assuma quando houver um único candidato na ficha e registre a origem como "inferida".
- **Erros de digitação e abreviações** ("sup inc", "extensora", "rdl", "hbc", "smith"): use correspondência aproximada sobre nomes oficiais + apelidos do usuário e confirme quando a confiança for baixa. Depois da confirmação, ofereça transformar a forma usada em apelido do usuário, respeitando a regra de não aceitar nomes ambíguos.
- **Exercício fora do catálogo:** registrar como "exercício não catalogado" com o texto original e as séries já interpretadas (reps, carga, unidade), pendente de vínculo. Assim nada se perde e, quando o usuário confirmar, a série é religada ao exercício (novo ou existente). Não crie exercício oficial automaticamente.

## 8. Tabela-resumo

| Item | Decisão recomendada | Frequência no relato (estimativa, sem fonte) | Seguro só como texto? |
|---|---|---|---|
| Forma da carga | Padrão no exercício + forma declarada e origem na série | Alta (todo relato com carga) | Não; guardar o texto como prova |
| Variações | Exercícios separados + atributos + grupo de variação | Alta | Não |
| "20 cada (lado)" | Explícito por lado/mão | Média-alta com halter/anilha | Não |
| "40 no total" | Explícito total; derivar por lado marcado | Baixa | Não |
| "2x20" | Séries × reps por padrão; perguntar se houver outro NxM | Alta como séries × reps; baixa como carga | Sim, até confirmar |
| Barra incluída? | Total só com peso da barra conhecido | Média | Parcial (anilhas sim; total não) |
| Polia/cabo | Número da pilha como está; comparável só no mesmo aparelho | Alta | Não |
| "Placa/pino N" | Unidade própria, sem kg | Média em máquinas | Sim (com unidade) |
| Smith | Anilhas + peso da barra por academia, se informado | Média | Parcial |
| Peso corporal/assistido | Tipos distintos: corpo, lastro, assistido | Média | Não |
| Elástico | Cor/nível como texto | Baixa | Sim |
| kg x lb | Converter guardando o original | Baixa no Brasil | Não |
| Apelidos ambíguos | Nunca globais; confirmar | Alta | Não |
| Fora do catálogo | "Não catalogado" + séries interpretadas | Baixa-média | Sim, temporariamente |

## 9. Classificação e limites das fontes

- **Científicas/revisadas:** estudos de nomenclatura de exercícios (Revista Brasileira de Cineantropometria e Desempenho Humano; Revista de Atenção à Saúde/USCS; Nuzzo et al., Strength and Conditioning Journal); ensaio biomecânico sobre voadores (Revista de Educação Física, 2016).\[52\]\[62\]\[63\]\[64\] São ensaios teóricos e questionários, não padrões técnicos.
- **Acadêmicas não revisadas ou institucionais:** Revista Educação Física UNIFAFIBE; material do CREF-SP.
- **Documentação técnica de apps e bases:** central de ajuda de Hevy, Strong, Fitbod e StrengthLog; README do wger e do free-exercise-db; termos do ExerciseDB e da MuscleWiki; páginas de fabricantes (Life Fitness, Matrix). São confiáveis sobre o próprio produto, mas comerciais.
- **Comerciais/blogs:** glossários e blogs brasileiros (Nextfit, Treino Mestre, hipertrofia.org, Integralmedica, TotalPass, Suplemento PE, Academia Central Fitness), blogs de lojas sobre a barra Smith e comparativos de APIs escritos por concorrentes (RepDB, MuscleWiki, AIFitnessAPI), que têm interesse próprio.
- **Comunitárias:** fóruns do JEFIT (opinião de usuários, não regra do app).\[7\]
- **Não encontrado ou não consultado:** posicionamentos de ACSM/NSCA sobre a convenção de registro de carga (não localizei nenhum específico); documentação de modelo de dados de Strong e JEFIT; razões de polia de Technogym e Hoist; peso padronizado de barras W/retas e de plataformas de leg press; estudos sobre variação regional de gírias de academia no Brasil (não encontrei nenhum, e as divergências sobre "cavalinho" podem ser regionais, mas não há fonte que o confirme); o termo "lag".

## 10. Recomendações práticas

1. **Mantenha a forma fixa no exercício**, mas acrescente à série: texto original, forma declarada, origem (explícita/derivada/padrão) e, opcionalmente, local/equipamento.
2. **Mantenha exercícios separados**, com atributos (movimento-base, equipamento, pegada, ângulo, unilateral, tipo de registro) e um grupo de variação no estilo do wger.
3. **Defina "tipo de registro" por exercício**, como no Hevy: peso & reps, corporal, corporal com lastro, assistido, duração. Isso evita ler "assistida 20" como carga.
4. **Leia "NxM" como séries × reps**; trate "2x20" como carga só com um segundo "NxM" e confirmação.
5. **Não converta unidades de máquina** ("placa", "pino", "meia anilha") em kg. Guarde com unidade própria.
6. **Para barra, Smith e leg press**, registre anilhas por lado e calcule o total só com peso de barra/plataforma cadastrado.
7. **Avise o usuário** quando o histórico misturar academias ou aparelhos de cabo diferentes, em vez de comparar números silenciosamente.
8. **Para desambiguar, liste candidatos da própria ficha**; nunca aceite como apelido termos ambíguos ("remada", "puxada", "pulley", "scott", "cavalinho", "crucifixo").
9. **Use free-exercise-db (domínio público) como seed opcional** de músculos/equipamento; use o wger só com controle de licença por item; evite ExerciseDB/MuscleWiki no MVP.

## 11. Decisões que o autor precisa tomar

1. Afundo/búlgaro e outros exercícios de perna com dois halteres serão POR_LADO ou TOTAL? StrengthLog e Strength Level divergem.
2. Em exercícios com barra, o total inclui a barra? E o que fazer quando o peso da barra é desconhecido?
3. A série terá um campo de local/equipamento (academia) para separar históricos de Smith/cabo?
4. Assistência (barra/paralela assistida) será um tipo próprio ou carga negativa?
5. "Placa/pino" ficará como unidade própria permanente ou o usuário poderá cadastrar a equivalência em kg por máquina?
6. Exercícios "não catalogados" expiram ou ficam pendentes até o vínculo?
7. Vai importar dados do wger? Se sim, como guardar e exibir a atribuição por item e cumprir o CC-BY-SA?

## Fontes

1. [Fitbod Metrics & Records](https://help.fitbod.me/hc/en-us/articles/12732749777047-Fitbod-Metrics-Records)
2. [How to Log Dumbbell Weight: Per Hand or Combined?](https://www.gymr-app.com/blog/log-dumbbell-weight)
3. [How are dumbbell exercises counted? - Strong Help Center](https://help.strongapp.io/article/101-how-are-dumbbell-exercises-counted)
4. [How Hevy Trainer Settings Work](https://help.hevyapp.com/hc/en-us/articles/43572343844247-How-Hevy-Trainer-Settings-Work)
5. [One-sided Exercises (”Enter for only one side”) - StrengthLog Support Area](https://help.strengthlog.com/help-article/one-sided-exercises/)
6. [I do all of my exercises with dumbbells and my question is related to logging my sets Should I be logging the weight of each dumbbell, the total weight lifted, or is there no "right" way to do this, meaning it's up to your personal preference? I'm currently logging the weight of each dumbbell](https://www.jefit.com/q&a/95205501/chad2584/i-do-all-of-my-exercises-with-dumbbells-and-my-question-is-related-to-logging-my-setsshould-i-be-logging)
7. [Q&A](https://www.jefit.com/q&a/98799008/jayfroman/question-regarding-counting-weight-and-reps-when-doing-say%E2%80%A6dumbbell-press-do-you-track-total-weight-of-both-dumbbells-together-or)
8. [Q&A](https://www.jefit.com/q&a/113791673/FitInMy50s/how-do-y%E2%80%99all-record-the-weight-per-set-when-using-dumbbells--for-example-if-i-do-bench-press-curls)
9. [Dumbbell (DB) Exercises: Should I Log One or Both Weights?](https://strongstrongfriends.zendesk.com/hc/en-us/articles/22345388150164-Dumbbell-DB-Exercises-Should-I-Log-One-or-Both-Weights)
10. [Getting Started!](https://help.fitbod.me/hc/en-us/sections/360001927994-Getting-Started)
11. [REST API - Api Root](https://wger.de/api/v2/)
12. [Wger Exercise Database Scraper · Apify](https://apify.com/jungle_synthesizer/wger-exercise-database-api-scraper)
13. [wger API — Free Exercise Database & Fitness API](https://bytetools.bytevancer.com/public-apis/sports/wger-fitness-api)
14. [156 Revista Educação Física UNIFAFIBE, Bebedouro/SP - Vol. VI– setembro/2018.](https://www.unifafibe.com.br/revistasonline/arquivos/revistaeducacaofisica/sumario/73/14092018191140.pdf)
15. [SELO COMEMORATIVO REGULAMENTAÇÃO DA PROFISSÃO DE EDUCAÇÃO FÍSICA 20 ANOS](https://www.crefsp.gov.br/storage/app/arquivos/e6edcacac83271cbe40faa7685e5164c.pdf)
16. [Nomes dos equipamentos da academia: lista com todos - Suplemento PE](https://suplementopernambuco.com.br/nomes-dos-equipamentos-da-academia/)
17. [Vocabulário de academia em inglês](https://www.inglesonline.com.br/vocabulario-de-academia-em-ingles/)
18. [Vocabulário de academia em inglês](https://centraldoingles.com/portal/vocabulario/vocabulario-de-academia-em-ingles/)
19. [Voador](https://pt.wikipedia.org/wiki/Voador)
20. [Aparelhos de Academia Nomes e Funções \[Lista Completa\]](https://appbefit.com/pt-br/blog/musculacao/nomes-de-aparelhos-de-academia/)
21. [Nomes dos aparelhos de academia: todos que você precisa saber](https://blog.nextfit.com.br/nomes-aparelhos-academia/)
22. [Pulley Frente: Como fazer, músculos, variações e Dicas](https://treinomestre.com.br/pulley-frente/)
23. [Pulley frente: que exercício é esse, para que serve e como fazer?](https://blog.ciaathletica.com.br/definicao/pulley-frente/)
24. [O Que é: Pulley Frente - Entenda Seu Funcionamento](https://coreus.com.br/glossario/o-que-e-pulley-frente-como-funciona-e-seus-beneficios/)
25. [Treino de costas: os 10 melhores exercícios para costas](https://uniguacu.com.br/blog/10-exercicios-para-treino-de-costas/)
26. [BRAÇO FORTE MUSCULAÇÃO: Dicas de Treino: Remada Cavalinho](http://bracofortemusculacao.blogspot.com/2011/12/dicas-de-treino-remada-cavalinho.html?m=1)
27. [11 Exercícios para Costas: Treino Dorsal Completo 2026](https://treinomestre.com.br/melhores-exercicios-para-costas-dorsais/)
28. [Remada Cavalinho: Como Fazer, Benefícios e Variações \[2025\]](https://www.academiacentralfitness.com.br/post/remada-cavalinho-como-executar-beneficios-e-variaoes)
29. [Remada cavalinho](https://www.evoxfitness.com.br/remada-cavalinho)
30. [Tríceps testa: formas de executar, cuidados e mais!](https://blog.integralmedica.com.br/triceps-testa/)
31. [Tríceps testa: músculos trabalhados, execução e variações](https://treinomestre.com.br/triceps-testa-como-executar-corretamente/)
32. [Fazer rosca testa ou francesa no treino de tríceps?](https://www.hipertrofia.org/blog/2025/09/20/fazer-rosca-testa-ou-francesa/)
33. [Tríceps francês: aprenda a fazer corretamente](https://totalpass.com/br/blog/atividade-fisica/triceps-frances-aprenda-a-fazer/)
34. [GitHub - wger-project/wger: Self hosted FLOSS fitness/workout, nutrition and weight tracker · GitHub](https://github.com/wger-project/wger)
35. [Contribution: 415 Brazilian Portuguese exercise translations (AI-assisted, human-sampled) · Issue #2555 · wger-project/wger](https://github.com/wger-project/wger/issues/2555)
36. [GitHub - yanroch/avocagym-frontend · GitHub](https://github.com/yanroch/avocagym-frontend)
37. [Exercise API — searchable exercise database with instructions · ReefAPI](https://reefapi.com/fitness-api)
38. [exercisedb api](https://github.com/JakobDrist/exercisedb-api)
39. [GitHub - bryanprimus/exercisedb-api · GitHub](https://github.com/bryanprimus/exercisedb-api)
40. [ExerciseDB vs wger: Which Exercise API? - AIFitnessAPI](https://aifitnessapi.com/compare/exercisedb-vs-wger)
41. [ExerciseDB FAQ - EDB Exercise Intelligence, GIFs, and License](https://exercisedb.io/faq)
42. [ExerciseDB Pricing](https://exercisedb.io/pricing)
43. [API Terms of Use](https://edb-docs.up.railway.app/docs/api-terms-of-use)
44. [MuscleWiki API vs ExerciseDB: Catalogue, Video and Languages](https://api.musclewiki.com/compare/exercisedb)
45. [MuscleWiki API — Exercise Database with Video Demonstrations](https://api.musclewiki.com/)
46. [Exercise Details Screen: Sets, Reps, and Weight Fields Explained](https://help.fitbod.me/hc/en-us/articles/29486697282711-Exercise-Details-Screen-Sets-Reps-and-Weight-Fields-Explained)
47. [Buy the Life Fitness Signature Series Dual Adjustable Pulley Online](https://www.topfitness.com/products/life-fitness-signature-series-dual-adjustable-pulley)
48. [Functional Trainer](https://us.matrixfitness.com/eng/strength/multi-station/g3-msft3-functional-trainer)
49. [SIGNATURE - Crossover exercise pulley by Life Fitness](https://www.medicalexpo.com/prod/life-fitness/product-113129-1134472.html)
50. [Functional Trainer](https://www.legendfitness.com/product/functional-trainer/)
51. [Inspire FT1 Functional Trainer](https://inspirefitness.com/products/ft1-functional-trainer)
52. [Revista de Educação Física (Sep 2016)](https://doaj.org/article/eb86a05631b04ecdb83cc28cc77e6cb9)
53. [Magnum Smith Machine](https://es.matrixfitness.com/esp/strength/plate-loaded/mg-pl62-smith-machine)
54. [Barra Guiada Smith com Contra-Peso](https://www.profitness.com.br/Barra-Guiada-Smith-com-Contra-Peso-Multifuncionais/1298/)
55. [Quanto pesa a barra na máquina Smith? Segredos](https://translate.google.com/translate?u=https%3A%2F%2Fxcelerategyms.com%2Fhow-much-does-the-bar-weigh-on-a-smith-machine%2F&hl=pt&sl=en&tl=pt&client=srp)
56. [Quanto pesa a barra da máquina Smith? Influencia as ...](https://www.hsefitness.com/how-much-does-smith-machine-bar-weigh-influence-synergies/)
57. [¿Cuánto pesa la barra de la máquina Smith en promedio?](https://gym-mikolo.com/blogs/home-gym/how-much-does-the-average-smith-machine-bar-weigh)
58. [¿Cuánto Pesa la Barra de una Máquina Smith](https://gym-mikolo.com/blogs/home-gym/smith-machine-bar-weight)
59. [Bodyweight Exercises in Hevy: Bodyweight vs Assisted vs Weighted](https://help.hevyapp.com/hc/en-us/articles/38386262243223-Bodyweight-Exercises-in-Hevy-Bodyweight-vs-Assisted-vs-Weighted)
60. [Assisted Bodyweight Exercises: How it Calculates Volume and PRs](https://help.hevyapp.com/hc/en-us/articles/34380762441111-Assisted-Bodyweight-Exercises-How-it-Calculates-Volume-and-PRs)
61. [Weighted Bodyweight Exercises: Is Bodyweight Included in Volume?](https://help.hevyapp.com/hc/en-us/articles/38387131819799-Weighted-Bodyweight-Exercises-Is-Bodyweight-Included-in-Volume)
62. [SciELO - Brazil - Comparison study of resistance exercise nomenclature adopted among professionals and undergraduate physical education students Comparison study of resistance exercise nomenclature adopted among professionals and undergraduate physical education students](https://www.scielo.br/j/rbcdh/a/hk4L5krygX69MX88b5S9XZJ/abstract/?lang=pt)
63. [Associações entre o tempo de experiência profissional e a nomenclatura dos exercícios utilizadas no treinamento de força](https://seer.uscs.edu.br/index.php/revista_ciencias_saude/article/view/4110)
64. [(PDF) Towards Standardization of the Nomenclature of Resistance Training Exercises](https://www.researchgate.net/publication/235728991_Towards_Standardization_of_the_Nomenclature_of_Resistance_Training_Exercises)
