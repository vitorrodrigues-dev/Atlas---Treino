# Levantamento de requisitos

Fase 1 (Planejamento). Este documento registra a pesquisa feita com praticantes antes de escrever as user stories e o escopo do MVP.

## Objetivo da pesquisa

Entender como as pessoas registram o treino hoje, o que as incomoda e como prefeririam registrar, para decidir o que o MVP precisa cobrir. A pesquisa levanta hipóteses; ela não prova demanda.

## Método

- Instrumento: formulário do Google de 2 minutos, com 9 perguntas: 6 de escolha, 1 aberta (relato de um exercício do último treino), 1 de interesse em conversar e 1 de nome (só para quem respondeu sim).
- Envio: 23/10/2026, por mensagem direta a praticantes do círculo do autor. Prazo de resposta: 25/10/2026.
- Respostas: 20, depois de remover a resposta de teste do autor. Um nome aparece em duas respostas; ambas foram mantidas porque não há como confirmar duplicidade.
- Perguntas de múltipla escolha aceitam mais de uma opção, por isso alguns totais passam de 20.

## Limites da amostra

- Amostra de conveniência: pessoas que o autor sabe que treinam, de uma cidade pequena.
- Perfil: pela observação do autor (o formulário não mediu), a maioria treina por saúde e poucos treinam com foco em desempenho ou progressão de carga. O formulário não perguntou o objetivo do treino nem se a pessoa treina com ficha.
- Por isso a pesquisa descreve como essas pessoas registram hoje, mas não comprova que o problema que o Atlas quer resolver existe para o público-alvo.
- Quem respondeu já tem interesse no tema; quem não treina ou não gosta de tecnologia está ausente.
- n = 20: serve para achar padrões e hipóteses, não para calcular proporções com segurança.
- Nomes e contatos ficam fora do repositório (LGPD). Aqui entram só dados agregados.

## Resultados

### Frequência de treino
| Resposta | Quantidade |
|---|---|
| 1 a 2 vezes por semana | 1 (5%) |
| 3 a 4 vezes | 10 (50%) |
| 5 ou mais | 9 (45%) |

### Como registram hoje
| Resposta | Quantidade |
|---|---|
| Não registro | 13 (65%) |
| Bloco de notas do celular | 4 (20%) |
| App de treino | 2 (10%) |
| Planilha | 1 (5%) |
| Caderno | 0 |

### O que mais incomoda no registro
| Resposta | Quantidade |
|---|---|
| Nada me incomoda | 8 (40%) |
| Esqueço de anotar | 5 (25%) |
| Perde tempo entre as séries | 3 (15%) |
| Não tenho onde ver minha evolução | 2 (10%) |
| Difícil achar o treino anterior | 1 (5%) |
| O app é complicado | 0 |
| Outra opção | 1 (5%) |

### Quando sentem falta do histórico
| Resposta | Quantidade |
|---|---|
| Para ver minha evolução | 10 (50%) |
| Para decidir a carga da série | 5 (25%) |
| Nunca senti falta | 5 (25%) |
| Para comparar treinos | 3 (15%) |
| Para treinar com segurança, sem exagerar na carga | 2 (10%) |
| Para saber o que fiz na semana passada | 1 (5%) |

### Técnicas que usam
| Resposta | Quantidade |
|---|---|
| Drop set | 10 (50%) |
| Nenhuma | 7 (35%) |
| Bi-set | 2 (10%) |
| Série de aquecimento anotada | 2 (10%) |
| Tri-set | 1 (5%) |
| Rest-pause | 1 (5%) |

### O que seria mais fácil no meio do treino
| Resposta | Quantidade |
|---|---|
| Tocar botões | 7 (35%) |
| Mandar texto | 5 (25%) |
| Mandar áudio | 4 (20%) |
| Não sei | 4 (20%) |

### Interesse em conversar sobre treino
14 responderam "sim", 5 "não" e 1 não respondeu. Os contatos ficam fora do repositório.

### Relato livre (15 respostas)
Padrões observados nos relatos:
- Faixas de repetição ("6 a 8") em vez de um número.
- Carga vaga ("carga moderada", "progredindo carga") ou ausente.
- Carga que muda a cada série.
- Falha descrita em palavras ("no limite", "quase não saiu", "só consegui até a 9ª").
- Aquecimento citado no meio do relato.
- Erros de digitação e abreviações.
- Um relato em formato de resumo, com totais calculados e emojis.

## O que a pesquisa indica

1. **O concorrente é não anotar.** 65% não registram. O sucesso do bot se mede por quantas dessas pessoas passam a registrar, não por quantas trocam de ferramenta.
2. **Evolução é o valor mais pedido** (50%), mas só 10% reclamam de não ter onde ver. Desejo declarado não é dor sentida: testar se quem diz querer ver a evolução realmente consulta. Isso sustenta a US-19 no MVP, com a ressalva de que ainda é hipótese.
3. **A dor com o registro não foi comprovada nesta amostra.** 40% dizem que nada incomoda e 65% não registram. Quem não registra pode nunca ter sentido necessidade. Hipótese: a dor aparece para quem treina com progressão de carga, grupo pequeno na amostra. A queixa mais comum é esquecer de anotar (25%), o que sugere um lembrete depois do treino como ideia futura.
4. **Drop set é comum** (50%) e exige modelo próprio (US-17). Aquecimento anotado é raro (10%), então a evidência para a US-18 é fraca: revisar se fica no MVP.
5. **Botões lideram na preferência de entrada** (35%), à frente de texto (25%) e áudio (20%). Texto mais áudio somam 45%, e a amostra é pequena. Hipótese: botões para confirmar, desfazer e resolver dúvidas; texto livre para os dados do treino.
6. **Os relatos são heterogêneos.** O extrator precisa aceitar faixas, carga vaga e erros de digitação, e deixar vazio o que não foi dito, sem inventar (RNF-01).
7. **Há interesse para teste:** 14 de 19 toparam conversar.

## Decisões em aberto geradas pela pesquisa

Decididas no `05-escopo-do-mvp.md` e em `decisoes/`: botões apenas para desfazer e resolver dúvidas, sem botão "Confirmar" (27/10/2026); US-19 (evolução por comando) no MVP; US-18 (aquecimento) no MVP (26/10/2026).

## Tratamento dos dados do formulário

- O formulário coletou nome apenas de quem aceitou conversar. As respostas ficam na conta do autor, fora do repositório.
- [verificar] Texto de consentimento do formulário.
- Descarte dos nomes: [definir data], depois das conversas de acompanhamento.

## Próximo passo de pesquisa

Conversas curtas, individuais, com quem aceitou conversar, priorizando quem treina com ficha ou com foco em progressão de carga. O objetivo é saber se a dor existe para esse perfil antes de abrir o teste. É mais barato e mais informativo do que repetir o formulário com mais gente.

## Relação com os outros documentos

- User stories: `03-user-stories.md`
- Escopo do MVP: `05-escopo-do-mvp.md`
