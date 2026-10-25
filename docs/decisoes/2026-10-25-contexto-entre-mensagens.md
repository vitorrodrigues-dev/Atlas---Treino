# Contexto entre mensagens fica no MVP

Data: 25/10/2026

## Contexto
Testadores tendem a relatar o treino aos poucos, uma série por vez, e o formulário trouxe relatos soltos e incompletos.

## Decisão
O bot guarda o contexto entre mensagens (US-02) no MVP.

## Alternativas consideradas
Ler cada mensagem isoladamente, deixando o contexto para o Pós-MVP.

## Consequências
É preciso definir por quanto tempo o contexto vale e o que fazer quando o exercício é ambíguo. Aumenta a complexidade do extrator.
