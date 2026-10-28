# Decisão: texto livre por série entra no MVP

Data: 27/10/2026
Status: aceita

## Contexto
Os relatos trazem informação sobre a série que o sistema ainda não estrutura: esforço percebido (RIR, RPE, "sobrou duas"), técnica ("roubei"), cadência, sensação ("senti que sairia mais uma"). A pesquisa de vocabulário mostrou que os termos mais frequentes (falha, até a falha) têm mais leituras possíveis, e que não há corpus de como brasileiros escrevem seus relatos de treino. Estruturar agora seria adivinhar.

## Decisão
1. Cada série tem um campo de **texto livre**, opcional, que guarda exatamente o que o usuário disse sobre aquela série e o sistema não estrutura.
2. O sistema **não interpreta nem deduz** a partir dele. "RIR 2" no texto não autoriza marcar `falha = não`.
3. A regra para o extrator: o que se refere à **série** vai para o texto livre da série; o que se refere ao **dia** ("hoje estou cansado") vai para a observação da sessão.
4. Estruturar algum termo (RIR, RPE, descanso) fica para depois, com base nos relatos reais da primeira semana de teste.
5. **Dado de saúde** ("ombro doendo") pode aparecer nesse campo. Ele segue o mesmo tratamento do texto bruto da mensagem (LGPD, aviso na boas-vindas, exclusão a pedido). O bot responde a dor como na US-10: anota sem diagnosticar e sugere procurar um profissional de saúde, sem recomendar o que fazer com a carga.

## Opções rejeitadas
- **Estruturar RIR/RPE já no MVP:** as definições são estáveis só quando o valor vem explícito, e ainda não sabemos o que os testadores escrevem.
- **Mandar tudo para a observação do dia:** perde a ligação com a série específica.
- **Ignorar o que não é estruturado:** o bot descartaria informação que o usuário quis registrar.

## Consequências
- O modelo de dados ganha um campo de texto livre na série.
- O prompt v4 ganha a regra "série x dia" e casos de regressão.
- O texto bruto da mensagem já fica salvo (RNF-02): o campo não cria um risco novo de dado de saúde, só uma segunda cópia do mesmo trecho.
