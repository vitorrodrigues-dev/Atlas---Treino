# Decisão: o usuário pode ter mais de uma sessão por dia

Data: 27/10/2026
Status: aceita

## Contexto
O repositório antigo permitia várias sessões na mesma data no banco, mas o MVP usava uma sessão por usuário por data. Quem treina duas vezes no dia (ex.: bodybuilder em preparação, treino de manhã e outro à noite) teria os dois treinos misturados numa sessão só. Ao mesmo tempo, "treino" no português falado tem dois sentidos: a sessão de hoje (o realizado) e o "treino A" da ficha (o planejado).

## Decisão
1. A **sessão** é o bloco de treino realizado, com data. Um usuário pode ter mais de uma no mesmo dia.
2. **Padrão:** a mensagem entra na sessão do dia. O bot só abre outra sessão quando o usuário disser explicitamente ("segundo treino", "treino da noite").
3. O bot **não** decide pelo horário nem por intervalo entre mensagens (seria inferência; RNF-01).
4. O **rótulo do treino** ("treino A", "treino B", "treino de peito") é uma etiqueta de texto opcional da sessão. O sistema não deduz exercícios a partir dele.
5. A **ficha** (plano prescrito) fica fora do MVP. A modalidade (musculação, corrida) também: no MVP, cardio vai como texto na observação da sessão.
6. A confirmação mostra em qual sessão a série entrou. Se entrou na errada, o `/desfazer` corrige.

## Opções rejeitadas
- **Uma sessão por usuário por data (regra antiga):** mistura dois treinos do mesmo dia e não cobre quem treina duas vezes.
- **Abrir nova sessão pelo intervalo de horário:** exige o bot inferir, e um treino longo seria dividido por engano.
- **Criar a entidade Ficha agora:** fora do escopo do MVP; o rótulo cobre o necessário sem ela.

## Consequências
- O modelo de dados permite várias sessões por usuário e data. A restrição de uma por data, se existir, sai.
- A US-03 (confirmação) mostra a sessão.
- O prompt v4 precisa de um caso de regressão para "segundo treino do dia".
