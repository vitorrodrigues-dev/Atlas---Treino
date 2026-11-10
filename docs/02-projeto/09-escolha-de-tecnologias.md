# Escolha de tecnologias

Fase 2 (Projeto). **Rascunho de 10/11/2026, redigido com assistência de IA (Claude). Ainda não lido nem aceito pelo autor.** Documento vivo.

Entradas: `07-arquitetura.md` (seção 8, a checklist) e `08-inventario-de-restricoes.md`. Cada escolha tem um **status**:

- **Decidida**: escolha fechada.
- **A confirmar**: depende do aceite deste documento.
- **Pendente**: falta informação ou verificação.

## 1. Resumo

| # | Item | Escolha | Status |
|---|---|---|---|
| T1 | Banco de dados | PostgreSQL. No MVP, em contêiner Docker no notebook (dentro do `docker compose`). Na expansão (front-end e VPS), Supabase. O Oracle da FIAP não entra como banco do Atlas | Decidida (PostgreSQL desde o início do MVP, Supabase na expansão). Contêiner local no MVP: a confirmar (alternativa: Supabase gratuito já no MVP) |
| T2 | Back-end | Java com Spring Boot: um processo contínuo que recebe o Telegram, chama o interpretador e grava. Sem endpoint público no MVP | Decidida |
| T3 | Acesso a dados | SQL escrito pelo autor, via JDBC (por exemplo, `JdbcTemplate`) sobre o `DataSource` do Spring. Sem JPA no MVP. Migrações versionadas (Flyway) para o DDL | A confirmar |
| T4 | Canal | Telegram, por consulta periódica (long polling), sem chamada de volta | A confirmar (o canal Telegram é herdado; o modo de entrega é novo) |
| T5 | Interpretador | API da Anthropic. Modelo candidato: Claude Haiku 5.5, identificador e versão fixa a verificar. Recarregar US$ 5 perto do início do teste, sem recarga automática | Decidida (recarga de US$ 5); modelo pendente |
| T6 | Front-end | Next.js com TypeScript, **depois do MVP**. O MVP não tem tela | Decidida |
| T7 | Empacotamento | Docker, com `docker compose` para subir tudo com um comando | Decidida |
| T8 | Hospedagem | MVP: o notebook do autor, ligado durante o teste. Expansão: plano de VPS da Hostinger, com Docker (a confirmar). Vercel só serviria a um front-end futuro | Decidida; preço pendente |
| T9 | Domínio | Contratar junto com a hospedagem da expansão, não antes: o MVP não precisa de endereço público | A confirmar |
| T10 | Qualidade | Testes de integração contra um PostgreSQL em contêiner, integração contínua no GitHub Actions, segredos só em variáveis de ambiente | A confirmar |

## 2. Conferência contra a checklist da arquitetura (§8)

| # | Exigência | Como esta pilha atende | Situação |
|---|---|---|---|
| 1 | Transações, unicidade, intervalos, chave composta | PostgreSQL tem as quatro coisas. Conferir R4 e R5 ao desenhar o modelo físico | Atende |
| 2 | Backup agendável, cópias de no máximo 30 dias | O plano gratuito do Supabase não inclui backup automático; o plano pago guarda 7 dias de cópias diárias. Solução: `pg_dump` semanal, descartando cópias com mais de 30 dias, vale para o contêiner local e para o Supabase. A cópia fica fora do notebook | **Pendente** (onde guardar) |
| 3 | Canal com entrega confirmável | Consulta periódica confirma ao pedir a atualização seguinte (A1). Não exige domínio nem HTTPS | Atende |
| 4 | Execução contínua com reinício controlado | Contêiner com política de reinício automático. No notebook, valem os cuidados do inventário (suspensão desativada, atualizações adiadas) | Atende |
| 5 | Segredos fora do repositório | Variáveis de ambiente passadas ao contêiner; arquivo `.env` no `.gitignore` | Atende |
| 6 | Saída estruturada, versão fixa, temperatura 0, recusa conhecida | Depende da verificação do modelo (inventário, seção 2) | **Pendente** |
| 7 | Testes de integração contra banco real | PostgreSQL em contêiner nos testes | Atende |
| 8 | Linguagem que o autor leia, explique e mantenha | Java e JDBC: sim. Spring Boot: em estudo na faculdade. TypeScript e Next.js: lacuna declarada, daí o adiamento do front-end | Atende, com a ressalva do T6 |
| 9 | Local dos dados e do processamento compatível com o aviso | Conferir a região escolhida no Supabase e o local de processamento da API; escrever os dois no aviso (US-11) | **Pendente** |

## 3. Riscos e consequências

- **Pilha nova.** Spring Boot, Docker, PostgreSQL e a API do Claude já são quatro tecnologias para o MVP. O inventário estima cerca de 3 meses com 2 horas por dia, no máximo 180 horas, sem contar faculdade e candidaturas. Deixar Next.js com TypeScript para depois do MVP (T6) mantém o primeiro marco viável; a tela entra como mais um serviço do `docker compose`.
- **Oracle da FIAP no MVP, depois migrar.** Traria dois conjuntos de scripts (tipos, identidade e texto diferem entre Oracle e PostgreSQL), uma migração depois e um acesso com data de fim desconhecida. Começar em PostgreSQL evita tudo isso e dá, desde o primeiro dia, a experiência com o banco que o autor quer ter. Decisão de 10/11/2026: o MVP começa em PostgreSQL.
- **Banco local no notebook.** Sem pausa, sem rede e sem custo, e o `docker compose up` sobe aplicação e banco juntos. Em troca, o notebook vira ponto único de falha: o backup semanal precisa sair dele. Os relatos podem trazer dado de saúde (inventário, seção 7), então o disco do notebook deve ser criptografado.
- **Quando o banco for para o Supabase.** O plano gratuito pausa projetos após uma semana sem atividade e não inclui backup automático. O plano pago (US$ 25 por mês, segundo a página de preços de 10/11/2026) inclui backup diário com 7 dias de retenção. Decidir o plano junto com a hospedagem da expansão.
- **Transação no Spring.** Anotar o caso de uso inteiro com `@Transactional` manteria a conexão aberta durante a chamada ao interpretador e violaria o A2 (duas transações, T1 e T2, com a chamada externa no meio). T1 e T2 precisam ser transações separadas.
- **ConnectionFactory.** O `DataSource` do Spring substitui a `ConnectionFactory` do repositório antigo. A regra A7 (a transação pertence ao caso de uso) continua valendo; muda só o mecanismo.
- **JPA.** Esconde o SQL. O projeto também serve para praticar JDBC e SQL, e o autor escreve o DDL e os DAOs. Por isso o T3 adota JDBC.
- **Docker não é hospedagem.** O contêiner roda onde houver um servidor. O bot é um processo contínuo, então serviços de funções sob demanda (Vercel, por exemplo) não servem a ele.
- **Custo.** A API custa centavos no teste (inventário, seção 2). O que pesa é a hospedagem depois do teste, sem número ainda.

## 4. Pendências

Nenhuma pendência impede começar a construção. Cada uma tem o marco em que precisa estar fechada.

| # | Pendência | Bloqueia | Fechar até |
|---|---|---|---|
| 1 | Confirmar que o notebook fica ligado e conectado durante todo o teste, ou definir o plano para quedas maiores que 24 horas (limite de retenção do canal) | Início do teste | Antes do teste |
| 2 | Validar o cronograma (construção em cerca de 3 meses, teste em janeiro) contra esta pilha | Plano da Fase 3 | Antes de começar a construção |
| 3 | Definir onde fica a cópia semanal do banco, fora do notebook | Início do teste | Antes do teste |
| 4 | Verificar o local de processamento da API e a região do banco, e escrevê-los no aviso de dados (US-11) | Abrir além dos testadores | Antes de abrir além dos testadores |
| 5 | Verificar identificador, versão fixa e comportamento em recusa do modelo (inventário, seção 2) | Fatia do interpretador | No spike do interpretador |
| 6 | Plano e preço da hospedagem e do Supabase da expansão | Expansão | Depois do MVP |

Decididos e ainda não refletidos no inventário (`08`): orçamento do MVP sem custo além dos US$ 5 de crédito da API, e Oracle da FIAP fora como banco do Atlas.

## Fontes (pesquisa de 10/11/2026)

- Supabase, planos e limites: https://supabase.com/pricing e https://supabase.com/docs/pricing
