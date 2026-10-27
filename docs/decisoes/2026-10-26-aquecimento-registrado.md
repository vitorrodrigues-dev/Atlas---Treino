# Decisão: aquecimento é registrado como tipo da série (US-18 entra no MVP)

Data: 26/10/2026
Status: aceita

## Contexto
A US-18 estava pendente (MVP ou Pós-MVP). O relato é texto livre, então o usuário vai mencionar aquecimento naturalmente. Sem regra, o extrator grava o aquecimento como série de trabalho e suja a última carga e a evolução. Além disso, "com quanto aqueci no leg press semana passada" é uma consulta legítima: o dado precisa existir.

## Decisão
1. A série tem um tipo: `TRABALHO`, `AQUECIMENTO` ou `PREPARATORIA` ("série de reconhecimento" é sinônimo de preparatória).
2. O bot não pergunta se a série é de aquecimento. Se o usuário disse, é aquecimento; se não disse, é trabalho. O bot não compara cargas nem deduz que a série "parece leve" (RNF-01: nunca deduz).
3. A confirmação mostra o tipo da série (nada quando é trabalho). É a rede de segurança; o `/desfazer` corrige.
4. `/carga` mostra todas as séries do último treino, com o tipo marcado.
5. A evolução (US-19) conta só séries de `TRABALHO`.
6. Aquecimento específico (cita exercício e carga ou repetições) vira série. Aquecimento geral (esteira, mobilidade) vai para a observação do dia, como o cardio.
7. "Aqueci com 20kg" sem exercício: o bot pergunta o exercício, ou assume o último citado se a janela de contexto (US-02, ainda a definir) deixar claro.
8. Gírias de academia (rest-pause, cluster etc.) não são tema do MVP. O prompt v4 trata os casos que aparecerem no teste real, com regressão. Exceção: drop set (US-17).

## Opções rejeitadas
- **MVP não registra aquecimento:** ainda exigiria o extrator distinguir aquecimento de trabalho, então paga a parte cara sem entregar o dado, e a consulta "com quanto aqueci" ficaria impossível.
- **Bot pergunta quando a série parece leve:** exige inferência (contra o RNF-01) e cria atrito em toda mensagem, o que contraria o texto livre.
- **Ignorar:** o aquecimento entraria como carga de trabalho e contaminaria a última carga, o PR e a evolução.

## Consequências
- O modelo de dados ganha a coluna de tipo da série (Fase 2).
- O prompt v4 e a regressão ganham casos de aquecimento e preparatória.
- US-03 e US-04 a revisar (a confirmação e o `/carga` passam a mostrar o tipo).
- O `05-escopo-do-mvp.md` precisa ser atualizado: US-18 sai de "pendente" e entra no MVP.
