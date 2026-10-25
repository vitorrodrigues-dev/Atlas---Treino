# ATLAS - User Stories

Documento vivo: as stories mudam conforme a pesquisa e o teste com pessoas reais. O Atlas está em planejamento e ainda não tem código. A fase de cada story segue o recorte definido em 05-escopo-do-mvp.md.

## Como ler este documento
- MVP: chatbot de texto, testado com pessoas aprovadas. Pós-MVP: o que só entra depois de o texto ser validado (áudio, site e telas). O recorte está em 05-escopo-do-mvp.md.
- Os itens sob cada story são os critérios de aceite: o que precisa ser verdade para a story contar como pronta.
- Valores marcados com [definir] ou [medir] dependem de uma decisão ou de uma medição e ainda não têm número.
- Escolhas de tecnologia ficam na fase de Projeto, não nas stories. Quando uma story cita canal, serviço ou infraestrutura, é uma restrição herdada de decisões anteriores e será revista na Fase 2.

## Personas
- Praticante com ficha (persona do MVP): treina com ficha ou acompanha a carga que usa e quer anotar sem fricção durante o treino. A persona parte do caso do autor: a pesquisa não perguntou se os respondentes treinam com ficha.
- Praticante iniciante (Pós-MVP): ainda não tem ficha nem vocabulário técnico e pode desistir da academia conforme a forma como é tratado. O tom do bot vale para todas as personas desde o MVP.
- Administrador: aprova testadores, acompanha o uso e apaga dados a pedido.

## Épico 1: Registrar o treino por conversa

### US-01 Relatar o treino em linguagem natural

Como praticante com ficha, quero relatar meu treino por texto, do jeito que falaria a um personal, para parar de anotar a carga no bloco de notas.

Fase: MVP.
- Dado um relato completo ("3x20kg no supino, falhei na última"), quando envio a mensagem, então as séries ficam salvas com exercício, carga e repetições.
- Dado que um dado não foi dito, quando o relato é salvo, então o campo fica vazio, nunca preenchido por suposição.
- Dado que a extração falhou, quando recebo a resposta, então o texto original continua salvo e sou avisado para reenviar.

### US-02 Relatar o treino aos poucos

Como praticante, quero mandar o treino em mensagens curtas, uma série por vez, para não escrever tudo de uma só vez no meio do treino.

Fase: MVP. Decidido em 25/10/2026: o bot guarda o contexto entre mensagens; falta definir por quanto tempo.
- Dado que citei o exercício há poucos minutos [definir a janela], quando envio "2ª série, 55kg", então a série vai para esse exercício e a confirmação mostra qual exercício foi assumido.
- Dado que não está claro a qual exercício a série pertence, quando envio a mensagem, então o bot pergunta em vez de assumir.

### US-03 Ver o que o bot entendeu

Como praticante, quero que o bot confirme o que registrou, para perceber na hora se ele entendeu errado.

Fase: MVP.
- Dado um relato registrado, quando recebo a confirmação, então ela mostra a data, o exercício e as séries, com "~" nos valores aproximados e "—" no que não foi dito (nunca 0).
- Dado um exercício fora do catálogo, quando recebo a confirmação, então o bot avisa que não o reconheceu e a série continua salva.

### US-17 Registrar um drop set como uma única série

Como praticante com ficha, quero relatar um drop set ("20 no supino, tirei peso e fiz mais 10, tirei de novo e fiz 8"), para que a série não vire várias séries soltas no histórico.

Fase: MVP. Aparece nos relatos do formulário.
- Dado um relato de drop set, quando envio, então ele fica como uma única série com etapas, cada uma com sua carga e repetições, na ordem em que foram ditas.
- Dado que a carga de uma etapa não foi dita, quando o relato é salvo, então ela fica vazia, nunca repetida da etapa anterior.
- Dado o drop set registrado, quando recebo a confirmação, então ela mostra as etapas separadas, para eu conferir.

### US-18 Separar o aquecimento das séries de trabalho

Como praticante com ficha, quero que as séries de aquecimento fiquem marcadas como aquecimento, para que não contem como minha carga de trabalho.

Fase: pendente (MVP ou Pós-MVP). Só 2 de 20 respondentes anotam aquecimento.
- Dado um relato com aquecimento ("aqueci com 20kg, depois 3x40"), quando envio, então a série leve fica marcada como aquecimento e as demais como de trabalho.
- Dado que não está claro se uma série é de aquecimento, quando o relato é salvo, então o bot pergunta em vez de decidir sozinho.
- Dado que consulto a última carga, quando vejo o resultado, então as séries de aquecimento não são tratadas como a carga de trabalho.

## Épico 2: Consultar treinos anteriores

### US-04 Consultar a última carga de um exercício

Como praticante, quero perguntar quanto levantei da última vez em um exercício, para não precisar decorar a carga no meio do treino.

Fase: MVP.
- Dado que registrei um exercício, quando envio /carga com o nome ou o apelido dele, então recebo todas as séries do último treino desse exercício.
- Dado um nome que serve para mais de um exercício, quando envio o comando, então o bot lista as opções em vez de escolher uma.
- Dado que existem registros desfeitos, quando consulto, então eles não aparecem.
- Dado que existem outros testadores, quando consulto, então vejo apenas os meus dados.

### US-05 Perguntar em texto livre

Como praticante, quero perguntar em linguagem natural (a carga da semana passada, o que treinei em determinado dia, quantas séries fiz), para ter um personal com boa memória.

Fase: Pós-MVP. É um épico e deve ser fatiado em carga por período, treino de um dia ou grupamento e volume de séries. Os critérios saem da análise do uso real.

### US-19 Consultar minha evolução por comando

Como praticante, quero perguntar como evoluí em um exercício ao longo das semanas, para ver meu progresso sem esperar o site.

Fase: MVP. Hipótese: a evolução é o valor mais citado na pesquisa (10 de 20), mas só 2 sentem falta de um lugar para ver. O uso desse comando será medido no teste. A tela de celular (US-14) continua Pós-MVP.
- Dado um exercício com registros em datas diferentes, quando envio o comando de evolução com o nome ou o apelido, então recebo a carga de trabalho por data, da mais antiga à mais recente [definir período].
- Dado menos de [definir mínimo] registros do exercício, quando consulto, então o bot informa que ainda não há dados suficientes.
- Dado registros desfeitos, quando consulto, então eles não aparecem.

## Épico 3: Corrigir erros

### US-06 Desfazer o último registro

Como praticante, quero desfazer meu último registro, para corrigir um erro sem apagar o histórico.

Fase: MVP.
- Dado que enviei um relato errado, quando envio /desfazer, então o registro é marcado como desfeito (não apagado) e a resposta lista o que saiu.
- Dado que envio /desfazer de novo, quando há registros anteriores nas últimas 24 horas, então o próximo registro é desfeito.
- Dado que o Telegram reenviou o mesmo comando, quando o bot o recebe, então nenhum segundo registro é desfeito.

## Épico 4: Acesso ao teste

### US-07 Entrar no teste sem criar conta

Como testador, quero abrir o bot e enviar /start, para começar sem criar conta nem senha.

Fase: MVP.
- Dado que ainda não estou cadastrado, quando envio /start, então fico pendente e recebo o aviso de que aguardo aprovação.
- Dado que fui aprovado, quando recebo o aviso de acesso liberado, então consigo registrar treinos.
- Dado que não fui aprovado ou fui bloqueado, quando envio mensagens, então nada meu é gravado.

### US-08 Aprovar testadores

Como administrador, quero aprovar cada testador, para saber quem tem dados no banco.

Fase: MVP (junto com a US-07).
- Dado um novo /start, quando ele chega, então recebo o nome e o comando de aprovação pronto.
- Dado que outra pessoa tenta aprovar, quando envia o comando, então ele não é reconhecido.
- Dado um testador, quando vou aprová-lo, então confirmo por outro canal que foi ele quem pediu e que é maior de 18 anos (regra do MVP).

## Épico 5: Tom e acolhimento

### US-09 Receber respostas acolhedoras

Como praticante, quero que o bot fale de forma simples e sem cobrança, para não me sentir julgado nem desanimar, principalmente quando falho uma série ou treino menos do que o planejado.

Fase: MVP. As frases do bot precisam ser escritas e revisadas antes do teste.
- Dado que registrei uma série com falha, quando recebo a confirmação, então ela não traz comentário de julgamento.
- Dado que o bot não entendeu, quando ele responde, então assume o erro ("não consegui entender") e não culpa o que eu escrevi.
- Dado qualquer resposta do bot, quando a leio no celular, então ela cabe em uma tela e não usa termo técnico sem necessidade.
- Dado o conjunto de frases do bot, quando dois testadores as leem, então nenhum aponta uma frase que soe como cobrança.

### US-10 Lidar com mensagens fora do assunto

Como administrador, quero que o bot tenha limites claros diante de xingamentos, assuntos sensíveis e pedidos fora do treino, para que ele não saia do papel nem dê conselho que não deve.

Fase: MVP. As proteções do modelo ajudam, mas o comportamento esperado do bot precisa ser definido e testado por nós.
- Dado um xingamento, quando o bot responde, então a resposta é curta e neutra, sem rebater, e nada é registrado como treino.
- Dado que relato dor ou lesão, quando o bot responde, então ele anota sem diagnosticar e sugere procurar um profissional.
- Dado um pedido de dieta, medicação ou diagnóstico, quando o bot responde, então ele recusa e indica um profissional.
- Dado um assunto sensível ou uma emergência, quando o bot responde, então ele não aconselha e indica buscar ajuda adequada [definir o texto].
- Dada uma lista de mensagens de teste desses casos, quando rodo a lista, então o comportamento esperado se repete (teste de regressão).

## Épico 6: Privacidade e conformidade

### US-11 Saber o que é gravado

Como testador, quero ser avisado do que é gravado e para onde vai, para decidir se quero usar.

Fase: MVP. O aviso deve informar que o texto passa pela API da Anthropic.
- Dado que me cadastro, quando recebo a boas-vindas, então ela informa que grava texto e treinos, que é um banco de estudo, que o texto passa pela API da Anthropic, que posso pedir para apagar e que o acesso depende de aprovação.
- Dado o aviso final, quando o reviso antes do teste, então ele cobre finalidade, dados de saúde que podem aparecer nas observações e direitos do titular, conforme a LGPD [decidir como validar].

### US-12 Pedir a exclusão dos meus dados

Como testador, quero poder pedir a exclusão dos meus dados, para ter controle sobre o que ficou guardado.

Fase: MVP. No início o administrador pode apagar direto no banco.
- Dado um pedido de exclusão, quando o administrador o atende em até [definir prazo], então mensagens, sessões e séries desse testador deixam de existir no banco.
- Dado que a exclusão foi feita, quando o testador pergunta, então ele recebe a confirmação.
- Dado o backup semanal, quando uma exclusão é feita, então a cópia do backup que contém esses dados também é tratada [definir como].

## Requisitos não funcionais

### RNF-01 Confiar no histórico

Como praticante, quero que o bot nunca invente o que não falei, para confiar no que está registrado.

Fase: MVP. O modelo não deve deduzir campos não ditos; isso vira regra do prompt do extrator.
- Dado um campo não dito, quando é gravado, então fica nulo no banco, nunca 0.
- Dado que o modelo deduziu um campo não dito (por exemplo, "sem falha"), quando o caso aparece nos testes, então ele entra na regressão do prompt.

### RNF-02 Não perder nem duplicar registros

Como praticante, quero que meus relatos não se percam nem se dupliquem se o bot cair, para não refazer anotações.

Fase: MVP.
- Dado que o bot ficou fora do ar, quando volta, então processa as mensagens pendentes sem duplicar.
- Dado que o texto chegou ao banco, quando a extração falha, então o texto original permanece salvo.

### RNF-03 Privacidade e segurança dos dados

Como testador, quero que meus dados só sejam vistos por mim e pelo administrador, para me sentir seguro ao usar.

Fase: MVP.
- Dado outro testador, quando ele consulta, então não vê meus dados.
- Dado o terminal e o log, quando o bot roda, então eles não mostram texto, nome ou identificador de usuário.
- Dado o repositório público, quando reviso antes de cada commit, então ele não contém chave, senha ou token.

### RNF-04 Respeitar o orçamento

Como administrador, quero que o custo da API fique dentro do orçamento, para não gastar além do planejado.

Fase: MVP.
- Dado o crédito pré-pago sem recarga automática, quando o saldo acaba, então o gasto para.
- Dado o teste de duas semanas, quando somo o custo, então ele fica dentro de R$ 50 por mês.

### RNF-05 Responder rápido e estar disponível

Como praticante, quero que o bot responda rápido, para não esperar entre as séries.

Fase: MVP. O tempo de resposta será medido no teste.
- Dado um relato enviado, quando o bot confirma, então a resposta chega em até [medir no teste] segundos.
- Dado que o notebook que hospeda o bot está desligado, quando envio uma consulta, então fico sem resposta; isso é aceito no teste e registrado como feedback.

## Pós-MVP

### US-13 Registrar por áudio

Como praticante, quero enviar áudio, para anotar o treino em dias corridos sem digitar.

Fase: Pós-MVP. Entra só depois de o texto estar estável e validado.
- Dado um áudio real, quando o envio, então ele vira as mesmas linhas que o texto equivalente.
- Dado que a transcrição falhou, quando o bot responde, então avisa e nada é perdido.

### US-14 Acompanhar minha evolução em uma tela de celular

Como praticante, quero ver minha evolução em uma tela limpa no celular, para acompanhar o progresso sem me perder em excesso de informação.

Fase: Pós-MVP. Só depois de o chatbot ser validado com pessoas reais. Primeiro para celular, depois para computador.
- Dado a tela principal, quando abro no celular, então ela responde uma pergunta por vez [definir qual] e a informação principal aparece sem rolar a tela.
- Dado qualquer tela, quando verifico contraste e tamanho do texto, então atendem ao nível AA da WCAG.
- Dado outro testador, quando acesso, então vejo apenas os meus dados [decidir como autenticar].

### US-15 Entender o bot por um site

Como testador novo, quero uma página que explique o bot e me leve ao Telegram, para entrar sem depender de explicação por mensagem.

Fase: Pós-MVP.
- Dado que abro a página no celular, quando leio, então entendo o que o bot faz e o que é gravado.
- Dado a página, quando toco no botão, então abro o bot no Telegram.

### US-16 Usar sem ficha de treino

Como praticante iniciante, quero usar o bot sem ter uma ficha pronta, para começar mesmo sem conhecer os exercícios.

Fase: Pós-MVP. Exige um catálogo genérico de exercícios e um tom pensado para quem está começando.

## Objetivos do desenvolvedor (não são requisitos do sistema)
- Aplicar método ágil: ciclo evolutivo com Kanban solo e limite de 1 a 2 tarefas em andamento.
- Documentar o processo desde a origem da ideia, para usar o projeto no portfólio e em entrevistas.
- Aprender a visão de engenheiro: dominar o ciclo de projeto inteiro e escrever a primeira versão do código, com a IA explicando e criticando.
