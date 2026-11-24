---
title: Aviso ao cliente por e-mail
status: draft
created: 2026-11-24
updated: 2026-11-24
---

# PRD: Aviso ao cliente por e-mail
*Nome provisório, confirmar.*

## 0. Propósito do documento

Este PRD orienta quem vai desenhar a experiência (UX), decidir a arquitetura e quebrar o trabalho em épicos e histórias para o FreelanceFlow. Ele parte do documento de intenção da sessão de brainstorming de 24/11/2026 (`_bmad-output/brainstorming/brainstorm-notificacao-email-movimento-tarefa-2026-11-24/brainstorm-intent.md`) e não repete o que está lá. Os termos do Glossário (§3) valem para o documento inteiro. As funcionalidades estão agrupadas em §4, com requisitos numerados de FR-1 em diante. Toda inferência sem confirmação do usuário está marcada como `[ASSUMPTION]` e listada em §10. Detalhe técnico (como enviar, onde guardar, qual provedor) está em `addendum.md`.

## 1. Visão

Hoje o freelancer avisa o cliente sobre o andamento do trabalho por WhatsApp, e-mail solto ou chamada, sempre de memória. O kanban do FreelanceFlow já sabe quando uma tarefa ficou pronta para revisão ou foi concluída, mas essa informação fica presa no navegador do freelancer.

O **Aviso ao cliente** faz o próprio quadro avisar: quando um cartão entra numa coluna que o freelancer marcou como pública, o cliente dele recebe um e-mail curto dizendo o que mudou. O freelancer não escreve nada, não esquece ninguém e pode desfazer um movimento errado antes de o e-mail sair.

Esta é a primeira peça de um ciclo maior (revisão, aprovação pelo cliente, fatura) que a sessão de brainstorming identificou como o valor real. Este PRD entrega só o aviso, mas desenhado para que a aprovação e a fatura possam ser encaixadas depois sem refazer nada.

## 2. Usuário-alvo

### 2.1 Jobs to be done

- **Freelancer, funcional:** manter o cliente informado sobre entregas sem gastar tempo escrevendo mensagens de status.
- **Freelancer, social:** parecer organizado e profissional aos olhos do cliente.
- **Freelancer, emocional:** parar de se preocupar com "esqueci de avisar o cliente que já está pronto?".
- **Cliente do freelancer, funcional:** saber quando há algo para revisar ou que foi entregue, sem precisar perguntar.

### 2.2 Não usuários (v1)

- Equipes ou agências com vários freelancers no mesmo quadro: o FreelanceFlow é de uso individual.
- O cliente como usuário do app: ele só recebe e-mails, não entra no FreelanceFlow.

### 2.3 Jornadas principais

- **UJ-1. Rafaela avisa o cliente sem escrever uma linha.**
  Rafaela é designer, atende 4 clientes e usa o FreelanceFlow no notebook. Ela termina o layout da página inicial do cliente Padaria Sol e arrasta o cartão para "Review". Aparece no canto: "Aviso para Padaria Sol sai em 60s. Desfazer". Ela não faz nada. Um minuto depois, o dono da padaria recebe um e-mail: "Rafaela moveu 'Layout da página inicial' para Review". No cartão, Rafaela vê "Aviso enviado às 14:02". **Caso de borda:** se ela tivesse arrastado para a coluna errada, clicaria em "Desfazer" e nenhum e-mail sairia.

- **UJ-2. Rafaela ativa o aviso pela primeira vez.**
  Rafaela vê o convite "Avisar seus clientes por e-mail quando um cartão chega em Review ou Done?" e clica em ativar. Como o envio automático precisa saber quem ela é, o app pede o e-mail dela e manda um link de acesso. Ela confirma pelo link. As colunas "Review" e "Done" ficam marcadas como públicas. Na próxima vez que mover um cartão de um cliente sem e-mail cadastrado, o app pede o e-mail do cliente ali mesmo, com a opção de pular.

- **UJ-3. Cliente pede para não receber mais.**
  O cliente Marcos acha que recebe e-mails demais. No rodapé do aviso ele clica em "Não quero mais receber avisos de Rafaela". A partir daí nenhum aviso sai para ele, e no cadastro do cliente Rafaela vê "Marcos pediu para não receber avisos".

- **UJ-4. Rafaela testa a ideia antes do envio automático existir (Fase 0).**
  Na Fase 0 não há envio automático. Quando Rafaela arrasta o cartão para "Review", aparece "Avisar Padaria Sol por e-mail?". Ela clica e o programa de e-mail dela abre com destinatário, assunto e texto já preenchidos. Ela revisa e envia ela mesma.

## 3. Glossário

- **Freelancer:** quem usa o FreelanceFlow e é dono do quadro.
- **Cliente:** cadastro de cliente do freelancer no app (entidade `Client`). Um cartão tem zero ou um Cliente.
- **E-mail do cliente:** endereço de e-mail opcional guardado no Cliente.
- **Cartão:** uma tarefa no quadro (entidade `Task`).
- **Coluna pública:** coluna que o freelancer marcou para gerar Aviso quando um Cartão entra nela. Toda coluna é pública ou não pública.
- **Aviso:** e-mail enviado ao Cliente informando que um ou mais Cartões dele entraram numa Coluna pública.
- **Janela de desfazer:** intervalo de 60 segundos entre o movimento do Cartão e o início do envio, durante o qual o freelancer pode cancelar o Aviso.
- **Agrupamento:** regra que junta num único Aviso os movimentos de Cartões do mesmo Cliente feitos dentro de 10 minutos.
- **Registro de envio:** estado do Aviso visível no Cartão: pendente, enviado, cancelado ou falhou.
- **Descadastro:** pedido do Cliente para não receber mais Avisos daquele Freelancer.
- **Conta de envio:** identificação mínima do Freelancer (e-mail confirmado por link) exigida apenas para o envio automático.

## 4. Funcionalidades

### 4.1 E-mail do cliente

**Descrição:** o Cliente ganha um campo opcional de e-mail. O Freelancer preenche no cadastro do Cliente ou no momento em que um Aviso precisaria dele. Realiza UJ-2.

#### FR-1: Cadastrar e-mail do cliente
O Freelancer pode informar, editar e apagar o E-mail do cliente no cadastro do Cliente.

**Consequências (testáveis):**
- Endereço sem formato válido de e-mail não é salvo e mostra mensagem de erro.
- Clientes já existentes continuam funcionando sem e-mail (o campo nasce vazio).

#### FR-2: Pedir o e-mail no momento certo
Quando um Cartão de um Cliente sem e-mail entra numa Coluna pública, o app pede o E-mail do cliente ali mesmo, com opção de pular. Realiza UJ-2.

**Consequências (testáveis):**
- Se o Freelancer informar o e-mail, ele é salvo no Cliente e o Aviso segue o fluxo normal.
- Se pular, nenhum Aviso é gerado para aquele movimento e o app não pergunta de novo para o mesmo Cliente na mesma sessão de uso.

### 4.2 Colunas públicas

**Descrição:** o Freelancer escolhe quais colunas geram Aviso. Existe um padrão pronto para quem não quer configurar nada. Realiza UJ-2.

#### FR-3: Marcar coluna como pública
O Freelancer pode marcar e desmarcar qualquer coluna como Coluna pública.

**Consequências (testáveis):**
- A marcação sobrevive a renomear a coluna.
- Coluna excluída e recriada nasce não pública.
- Colunas públicas têm um indicador visual no quadro.

#### FR-4: Ativação com um clique
Ao ativar o Aviso ao cliente, as colunas que nasceram como "Review" e "Done" no quadro inicial viram públicas automaticamente. `[ASSUMPTION: o padrão segue a coluna, não o nome; se o Freelancer já renomeou essas colunas, o padrão continua valendo para elas.]`

**Consequências (testáveis):**
- Depois da ativação, o Freelancer vê quais colunas ficaram públicas e pode mudar.
- Se essas colunas não existirem mais, nenhuma coluna fica pública e o app pede para escolher.

### 4.3 Envio do aviso

**Descrição:** quando um Cartão com Cliente entra numa Coluna pública, nasce um Aviso pendente. Ele espera a Janela de desfazer, é agrupado com outros movimentos do mesmo Cliente e então é enviado. Realiza UJ-1.

#### FR-5: Gerar aviso
Um Cartão que entra numa Coluna pública gera um Aviso pendente quando tem Cliente, o Cliente tem E-mail e o Cliente não fez Descadastro.

**Consequências (testáveis):**
- Mover um Cartão entre duas colunas não públicas não gera Aviso.
- Reordenar Cartões dentro da mesma coluna não gera Aviso.
- Cartão sem Cliente não gera Aviso.

#### FR-6: Janela de desfazer
Durante 60 segundos depois do movimento, o Freelancer vê "Aviso para {Cliente} sai em 60s. Desfazer" e pode cancelar. Realiza UJ-1.

**Consequências (testáveis):**
- Clicar em Desfazer cancela o Aviso e o Registro de envio mostra "cancelado".
- Mover o Cartão de volta para uma coluna não pública dentro da janela cancela o Aviso daquele Cartão.

#### FR-7: Agrupamento
Movimentos de Cartões do mesmo Cliente feitos dentro de 10 minutos viram um único Aviso, com o estado final de cada Cartão.

**Consequências (testáveis):**
- Três Cartões do mesmo Cliente movidos para "Review" em 5 minutos geram um e-mail com os três.
- Um Cartão que entrou e saiu de Coluna pública dentro dos 10 minutos não aparece no Aviso.

#### FR-8: Conteúdo do aviso
O Aviso traz o nome do Freelancer, o título de cada Cartão e a coluna para onde ele foi. A resposta ao e-mail vai para o e-mail do Freelancer. `[ASSUMPTION: o Aviso sai no idioma do app do Freelancer, pt ou en.]`

**Consequências (testáveis):**
- O Aviso não mostra horas, valores, descrição do Cartão nem colunas não públicas.
- O campo de resposta do e-mail é o e-mail da Conta de envio do Freelancer.

**Fora do escopo:**
- Botões de aprovar ou pedir ajuste (Fase 2).
- Texto editável pelo Freelancer.

#### FR-9: Sem aviso duplicado
Um mesmo movimento nunca gera dois Avisos, mesmo que o Freelancer use o app em dois navegadores.

**Consequências (testáveis):**
- Reenviar o mesmo evento de movimento ao serviço de envio não gera segundo e-mail.

### 4.4 Registro de envio e descadastro

**Descrição:** o Freelancer sabe o que aconteceu com cada Aviso, e o Cliente pode parar de receber. Realiza UJ-1 e UJ-3.

#### FR-10: Registro de envio no cartão
Cada Cartão mostra o Registro de envio do último Aviso: pendente, enviado (com horário), cancelado ou falhou.

**Consequências (testáveis):**
- Um Aviso que falhou mostra o motivo em linguagem simples (por exemplo, "e-mail do cliente inválido").

#### FR-11: Descadastro em um clique
Todo Aviso tem no rodapé um link para o Cliente parar de receber Avisos daquele Freelancer, sem login. Realiza UJ-3.

**Consequências (testáveis):**
- Depois do Descadastro, nenhum Aviso sai para aquele Cliente.
- O cadastro do Cliente mostra que ele pediu para não receber Avisos.
- O Freelancer não consegue reativar o envio sozinho; só um novo consentimento do Cliente reativa. `[ASSUMPTION]`

### 4.5 Conta de envio

**Descrição:** o envio automático acontece fora do navegador, então o serviço precisa saber de quem é cada Aviso. Quem não ativa o Aviso ao cliente continua usando o FreelanceFlow sem conta, como hoje. Realiza UJ-2.

#### FR-12: Ativar com conta mínima
Para ativar o envio automático, o Freelancer informa o próprio e-mail e confirma por um link de acesso. `[ASSUMPTION: login só por link no e-mail, sem senha.]`

**Consequências (testáveis):**
- Sem confirmar o link, nenhum Aviso automático é enviado.
- Desativar o Aviso ao cliente para todos os envios futuros e não afeta o resto do app.

### 4.6 Fase 0: aviso pelo e-mail do próprio freelancer

**Descrição:** antes do envio automático, uma versão sem servidor valida se freelancers realmente querem avisar clientes. Realiza UJ-4.

#### FR-13: Abrir e-mail já preenchido
Quando um Cartão com Cliente que tem E-mail entra numa Coluna pública, o app oferece "Avisar {Cliente} por e-mail". Ao clicar, abre o programa de e-mail do Freelancer com destinatário, assunto e texto preenchidos.

**Consequências (testáveis):**
- Nenhum dado sai do navegador por conta do app.
- Se o Freelancer ignorar a oferta, nada acontece.

#### FR-14: Contar uso localmente
O app conta, no próprio navegador, quantas vezes o Freelancer usou "Avisar por e-mail" por semana e mostra esse número nas configurações. `[ASSUMPTION: sem telemetria; a medição da Fase 0 depende de o Freelancer de teste compartilhar o número.]`

## 5. Requisitos não funcionais

- **NFR-1, prazo de entrega:** o Aviso é entregue ao provedor de e-mail em até 2 minutos depois do fim da Janela de desfazer e do Agrupamento.
- **NFR-2, dados mínimos:** só saem do navegador o título do Cartão, o nome da coluna, o nome e o E-mail do cliente, o nome e o e-mail do Freelancer e o identificador do movimento. Horas, valores e descrições nunca saem.
- **NFR-3, app sem conta continua igual:** quem não ativa o Aviso ao cliente não percebe mudança de desempenho nem precisa de internet para usar o quadro.
- **NFR-4, entregabilidade:** Avisos saem de domínio com autenticação de e-mail configurada para reduzir a chance de cair em spam.
- **NFR-5, privacidade (LGPD):** o E-mail do cliente é usado só para Avisos; Descadastro é respeitado em até 1 minuto; o Freelancer pode apagar o e-mail do Cliente a qualquer momento, e isso remove o dado do serviço de envio.

## 6. Fora dos objetivos

- O FreelanceFlow não vira ferramenta de e-mail marketing nem de comunicação geral com clientes.
- O cliente não ganha acesso ao app nem a uma página de status nesta versão.
- Nada de cobrança, fatura ou aprovação pelo e-mail nesta versão.
- Nada de WhatsApp ou outros canais.

## 7. Escopo

### 7.1 Fase 0 (validação, sem servidor)
- FR-1, FR-2, FR-3, FR-4, FR-13, FR-14.

### 7.2 Fase 1 (MVP com envio automático)
- Tudo da Fase 0, mais FR-5 a FR-12. FR-13 deixa de aparecer para quem ativou a Conta de envio.

### 7.3 Fora do MVP
- **Aprovar ou pedir ajuste pelo e-mail, com o cartão voltando sozinho:** é o próximo PRD (Fase 2).
- **Fatura em rascunho na aprovação e contador de rodadas de ajuste:** dependem da aprovação.
- **Resumo diário e semanal para o Freelancer:** valor alto, mas é outro destinatário; avaliar depois da Fase 1. `[NOTE FOR PM: foi uma das ideias que mais animou o usuário no brainstorming; revisitar logo após a Fase 1.]`
- **Página de status do projeto e entidade "projeto":** pedem um conceito novo no app.
- **Regra por cliente (cliente A recebe tudo, cliente B só entregas):** a Coluna pública resolve o caso comum.
- **Texto do Aviso editável, anexos, Pix, WhatsApp.**

## 8. Métricas de sucesso

**Primária**
- **SM-1:** freelancers que usam "Avisar por e-mail" pelo menos 2 vezes por semana durante 3 semanas seguidas na Fase 0. Meta: 3 de 5 freelancers de teste. É o critério para começar a Fase 1. Valida FR-13.
- **SM-2:** entre os freelancers ativos na Fase 1, a parcela que ativa o Aviso ao cliente. Meta: 30% em 60 dias. Valida FR-4, FR-12.

**Secundária**
- **SM-3:** Avisos enviados por semana por freelancer que ativou. Indica uso recorrente. Valida FR-5 a FR-8.
- **SM-4:** parcela de Avisos que falham. Meta: abaixo de 2%. Valida FR-10, NFR-4.

**Contramétricas (não otimizar)**
- **SM-C1:** volume total de e-mails enviados. Mais e-mail não é melhor; o Agrupamento existe para reduzir. Contrabalança SM-3.
- **SM-C2:** taxa de Descadastro. Acima de 5% dos Clientes indica que o Aviso virou ruído. Contrabalança SM-2 e SM-3.
- **SM-C3:** taxa de Desfazer. Acima de 15% indica que o gatilho está disparando em movimentos que não deveriam avisar. Contrabalança SM-3.

## 9. Perguntas em aberto

1. Como o app passa a ter um serviço de envio sem perder a simplicidade de guardar tudo no navegador? Só os eventos de movimento vão ao servidor, ou o estado inteiro é sincronizado? (Arquitetura; bloqueia a Fase 1, não a Fase 0.)
2. Qual provedor de e-mail transacional e de qual domínio os Avisos saem?
3. Qual o limite de Avisos no plano gratuito, se houver plano pago?
4. Quem são os 5 freelancers de teste da Fase 0 e como coletamos o número de SM-1?
5. O cliente deveria escolher o idioma do Aviso, em vez de herdar o do Freelancer?

## 10. Índice de suposições

- §4.2 FR-4: padrão de Colunas públicas segue as colunas que nasceram como "Review" e "Done", mesmo renomeadas.
- §4.3 FR-8: Aviso sai no idioma do app do Freelancer.
- §4.4 FR-11: Freelancer não reativa envio para Cliente que fez Descadastro.
- §4.5 FR-12: Conta de envio usa login por link no e-mail, sem senha.
- §4.6 FR-14: Fase 0 não tem telemetria; medição depende do Freelancer de teste.
- Geral: o destinatário do Aviso é o Cliente, não o próprio Freelancer (o pedido original dizia "ele").
- Geral: base legal LGPD é o legítimo interesse do Freelancer em informar o próprio Cliente, com Descadastro em um clique.
