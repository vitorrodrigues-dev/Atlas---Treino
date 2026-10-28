# Glossário de domínio: linguagem da musculação

> Os itens marcados [decidir] abaixo foram decididos em `01-definicoes-de-dominio.md` (27/10/2026).

Fase 2 (Projeto). Pesquisa de 27/10/2026, tópico 1: sessão, série e vocabulário em volta deles.

**Para que serve:** saber como as pessoas falam, para o extrator entender o relato e para o modelo de dados não usar o mesmo nome para coisas diferentes. Este documento é vocabulário, não decisão. As decisões vão em `01-definicoes-de-dominio.md` e em `decisoes/`.

**Como ler:** cada termo traz o que a pesquisa mostrou, as variações de fala e o que ainda precisa ser decidido. Itens marcados [decidir] são seus. Itens marcados [conhecimento geral] não vieram da pesquisa e precisam ser conferidos nos testes reais.

**Qualidade das fontes:** mistura de trabalhos acadêmicos, glossários de sites de fitness e blogs. Nenhum é autoridade única. Onde as fontes divergem, o documento diz.

---

## 1. Repetição (rep)
Uma execução completa do movimento, do início ao fim.
- Fala: "rep", "repetição", "vez".
- Aproximado ("mais ou menos 8", "7 a 8") já está tratado no repositório antigo: menor valor, marcado como aproximado.

## 2. Série (set)
Grupo de repetições seguidas, sem descanso no meio. O descanso vem depois.
- Fala: "série", "set". "3x10" significa 3 séries de 10 repetições.
- **Atenção:** "3x20kg" (do relato do MVP) quer dizer 3 séries com 20 kg, mas o número de repetições não foi dito.

## 3. Treino × sessão [decidir]
Nas fontes, "sessão" é um treino isolado, um bloco de trabalho, e "divisão de treino" é como os dias da semana se distribuem entre grupos musculares.
- **"Treino" é ambíguo no português falado** [conhecimento geral]: pode ser a sessão de hoje ("meu treino de hoje") ou o treino da ficha ("treino A", "treino de peito"). O modelo precisa de dois conceitos, ou o bot confunde.
- Pergunta para decidir: dois treinos no mesmo dia (manhã e noite) são uma sessão ou duas? No repositório antigo: uma por usuário por data.

## 4. Série de trabalho (working set)
A série feita na carga e na intensidade-alvo do dia, em oposição à série leve de aquecimento.
- Fala: "série válida", "série valendo", "série efetiva".

## 5. Aquecimento: geral × específico
As fontes acadêmicas brasileiras separam em duas coisas:
- **Geral:** atividade leve, sem carga definida (esteira, bicicleta, mobilidade).
- **Específico:** usa o próprio exercício com carga menor.
- Isso confirma a decisão de 26/10: aquecimento específico vira série; geral vai para a observação do dia.

## 6. Série de aproximação / reconhecimento / preparatória [decidir]
Nomes usados para séries entre o aquecimento e a de trabalho: a carga sobe aos poucos até chegar na carga de trabalho. Um material de iniciantes chama de "aproximação" e usa uma primeira série leve para "mapear" o movimento.
- **Nenhuma fonte define uma fronteira rígida** entre aquecimento, aproximação e preparatória. Cada autor usa de um jeito.
- Consequência para o bot: ele não deve classificar pela carga. Só marca o tipo quando o usuário usa a palavra (regra da decisão de 26/10).
- Decidir: "reconhecimento" e "aproximação" viram sinônimos de PREPARATORIA, ou ficam separados? Proposta: sinônimos, porque o usuário não vai distinguir.

## 7. Falha
Ponto em que não dá para fazer mais uma repetição com boa técnica (falha concêntrica).
- Fala: "falhei", "foi até a falha", "não subiu", "travei".
- **Terminologia ambígua:** um artigo científico (Steele et al., 2017) trata justamente da falta de clareza nos termos que indicam o fim de uma série (falha, quase falha, até a exaustão). Isso reforça que o bot só marca falha quando é dito.
- Casos já anotados: "tentei 8, consegui 6" é falha? [decidir no prompt v4].

## 8. Técnicas que mexem na estrutura da série

| Técnica | O que é | Carga | Pausa |
|---|---|---|---|
| **Drop set** | Vai até a falha, reduz a carga e continua logo em seguida. "Drop duplo" quando reduz duas vezes. | Cai a cada etapa | Quase nenhuma, só para tirar peso |
| **Rest-pause** | Faz a série até perto da falha, pausa curta, faz mais repetições. As pausas ficam dentro da série. | Normalmente a mesma | Curta (dezenas de segundos) |
| **Cluster** | Pausas planejadas antes da fadiga forte, para manter o desempenho. | A mesma | Curta, planejada |
| **Myo-reps** | Variante padronizada do rest-pause: série de ativação e várias minisséries. | A mesma | Curta |
| **Bi-set / super-set** | Dois exercícios alternados. Não é uma série com etapas. | Cada exercício a sua | Entre os exercícios |

- **Observação para o modelo de dados** [hipótese]: drop set, rest-pause, cluster e myo-reps têm a mesma forma, uma série com várias etapas (carga e repetições por etapa). A tabela `serie_etapa` do drop set talvez já sirva para todos, mudando só o rótulo da técnica. Confirmar na modelagem.
- No MVP só o drop set (US-17) entra. Bi-set e tri-set estão fora (decisão anterior).

## 9. Carga
Peso levantado. Duas formas na fala: total ("40 kg") e por lado ("20 de cada lado"). Fonte do problema: "20 cada lado" num exercício cuja ficha diz TOTAL. Isso é regra de consistência do catálogo, não vocabulário.

## 10. Termos que aparecem na fala mas ficam fora do MVP
RPE e RIR (esforço percebido e repetições na reserva), tempo de descanso, cadência, 1RM, volume, mesociclo, deload. Aparecem nos glossários e podem aparecer em relatos. O extrator deve ignorá-los sem inventar campo, e os casos reais viram regressão no prompt v4.

---

## Fontes consultadas
- Glossário "The Ultimate Glossary of Strength and Conditioning Terms" (Art of Manliness): rep, set.
- Glossário de força para público de resistência (the5krunner.com): working set, falha, intervalo de descanso.
- Glossário "Muscle & Strength": drop set, rest-pause, RPE.
- Glossário Alpha Progression: rest-pause, cluster, myo-reps, diferença para drop set.
- Dicionário fitness (Droga Raia): série, repetição, falha.
- Glossário GetFitCraft (PT): cluster sets, divisão de treino, RPE.
- Periódico FURG, "Respostas hemodinâmicas ao complexo de aquecimento na musculação": aquecimento geral × específico.
- Material de musculação para iniciantes (cursa.app): séries de aproximação.
- Steele, Fisher, Giessing, Gentil (2017), "Clarity in Reporting Terminology and Definitions of Set End Points in Resistance Training", Muscle & Nerve 56(3): ambiguidade de terminologia da falha.

## Próximos tópicos (um por vez)
- Exercícios: nomes, apelidos regionais, variações (catálogo).
- Carga e equipamento (halter, barra, polia, máquina, Smith).
- Ciclo de vida do registro (estados da mensagem).
