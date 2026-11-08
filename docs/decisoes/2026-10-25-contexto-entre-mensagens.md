# Contexto entre mensagens fica no MVP

Data: 25/10/2026

## Contexto
Testadores tendem a relatar o treino aos poucos, uma série por vez, e o formulário trouxe relatos soltos e incompletos.

## Decisão
O bot guarda o contexto entre mensagens (US-02) no MVP.

## Alternativas consideradas
Ler cada mensagem isoladamente, deixando o contexto para o Pós-MVP.

## Consequências
O contexto vale por 30 minutos (definido em 27/10/2026); o que fazer quando o exercício é ambíguo ficou na decisão 2 de 01/11/2026. Aumenta a complexidade do extrator.
