# Decisão: o relato é gravado na hora, com botão Desfazer

Data: 27/10/2026
Status: aceita. Substitui `2026-10-25-botoes-so-para-confirmar-e-desfazer.md`.

## Contexto
A decisão de 25/10 previa botões para confirmar, desfazer e resolver dúvidas. Ao definir o ciclo de vida do registro, ficou claro que um botão "Confirmar" faria cada relato esperar um toque antes de valer, o que cria atrito no meio do treino. No Telegram não existe desfazer nativo, então o usuário precisaria digitar `/desfazer`.

## Decisão
1. O relato é gravado na hora. A confirmação é informativa e traz o botão **Desfazer**. Não existe botão "Confirmar".
2. Os botões servem para desfazer e para responder a perguntas pendentes do bot.
3. Pergunta pendente: respondida por botão, validade de 30 minutos. Texto digitado nesse período é tratado como novo relato, e a pergunta expira. Ao expirar, nada é gravado como série e o texto bruto fica salvo.
4. O botão Desfazer atinge o registro da confirmação em que aparece e vale por 24 horas, como o comando `/desfazer`. Os dois são idempotentes.
5. A janela de contexto da US-02 também é de 30 minutos, ajustável depois da primeira semana de teste.

## Opções rejeitadas
- **Exigir um toque em "Confirmar" antes de gravar:** atrito em todo relato, e a confirmação já é visível.
- **Entrada do treino por botões:** exige muitas telas para séries, cargas e repetições (decisão de 25/10 mantida).

## Consequências
- O escopo, a US-03 e a US-06 mudam: sem "botões para confirmar", com o botão Desfazer.
- A métrica "relatos confirmados sem correção" vira "relatos sem /desfazer".
