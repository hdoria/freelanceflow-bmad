---
status: final
stepsCompleted: [1, 2, 3, 4]
inputDocuments:
  - _bmad-output/planning-artifacts/prds/prd-freelanceflow-bmad-2026-11-24/prd.md
  - _bmad-output/planning-artifacts/prds/prd-freelanceflow-bmad-2026-11-24/addendum.md
---

# FreelanceFlow: quebra em épicos do Aviso ao cliente por e-mail

## Visão geral

Este documento quebra em épicos e histórias os requisitos do PRD "Aviso ao cliente por e-mail" e do seu addendum técnico. Ainda não existe documento de arquitetura nem de UX: o addendum faz as vezes de arquitetura, e as histórias da Fase 1 que dependem de decisão do arquiteto estão marcadas.

## Inventário de requisitos

### Requisitos funcionais

FR1: O Freelancer pode informar, editar e apagar o e-mail do Cliente no cadastro; e-mail inválido não é salvo; clientes existentes continuam funcionando sem e-mail.
FR2: Quando um Cartão de Cliente sem e-mail entra numa Coluna pública, o app pede o e-mail ali mesmo, com opção de pular; ao pular, não gera Aviso e não pergunta de novo para o mesmo Cliente na mesma sessão.
FR3: O Freelancer pode marcar e desmarcar qualquer coluna como pública; a marcação sobrevive a renomear; coluna nova nasce não pública; colunas públicas têm indicador visual.
FR4: O Freelancer ativa o Aviso ao cliente nas configurações; o app sugere como públicas as colunas "Review"/"Done"/"Em revisão"/"Concluído" e pede confirmação; sem essas colunas, pede para escolher.
FR5: Cartão que entra em Coluna pública gera Aviso pendente quando tem Cliente com e-mail e sem Descadastro; movimentos entre colunas não públicas, reordenação e cartões sem Cliente não geram Aviso.
FR6: Depois de cada movimento que gera Aviso, o Freelancer vê "Aviso para {Cliente} sai em 2 min. Desfazer"; Desfazer retira só aquele Cartão; voltar para coluna não pública equivale a Desfazer; fechar a aba não cancela o Aviso.
FR7: Cartões do mesmo Cliente movidos durante a Janela de espera (2 min desde o último movimento, teto de 10 min) saem num único Aviso com o estado final de cada Cartão.
FR8: O Aviso traz o nome do Freelancer, o título de cada Cartão e a coluna de destino; resposta vai para o e-mail do Freelancer; não mostra horas, valores, descrição nem colunas não públicas; sai no idioma do app.
FR9: Um mesmo movimento nunca gera dois Avisos, mesmo com o app aberto em dois navegadores.
FR10: Cada Cartão mostra o Registro de envio do último Aviso (pendente, enviado com horário, cancelado, falhou com motivo); o app atualiza esse estado e o Descadastro ao abrir e a cada 5 min; sem internet mostra o último estado conhecido.
FR11: Todo Aviso tem link de Descadastro sem login; depois dele nenhum Aviso sai para o Cliente; o cadastro do Cliente mostra o pedido; o Freelancer não reativa sozinho.
FR12: Para o envio automático, o Freelancer informa nome e e-mail e confirma por link de acesso; sem confirmar, nada é enviado; desativar para envios futuros sem afetar o resto do app.
FR13: (Fase 0) Quando um Cartão de Cliente com e-mail entra em Coluna pública, o app oferece "Avisar {Cliente} por e-mail" e abre o programa de e-mail do Freelancer já preenchido; nenhum dado sai do navegador.
FR14: (Fase 0) O app conta no navegador quantas vezes por semana o Freelancer usou "Avisar por e-mail" e mostra o número nas configurações.

### Requisitos não funcionais

NFR1: O Aviso chega ao provedor de e-mail em até 2 minutos depois do fim da Janela de espera.
NFR2: Só saem do navegador título do Cartão, nome da coluna, nome e e-mail do Cliente, nome e e-mail do Freelancer e id do movimento; o serviço guarda e-mail do Cliente e Descadastro enquanto o Aviso estiver ativo.
NFR3: Quem não ativa o Aviso ao cliente não percebe mudança de desempenho nem precisa de internet.
NFR4: Avisos saem de domínio com autenticação de e-mail (SPF/DKIM) configurada.
NFR5: E-mail do Cliente usado só para Avisos; Descadastro respeitado em até 1 minuto; apagar o e-mail no app remove do serviço.

### Requisitos adicionais (do addendum, no lugar da arquitetura)

- Não há template inicial: o projeto já existe (React 18, Vite, TypeScript, shadcn/ui, Tailwind).
- Persistência só via `saveState`/`loadState`; campo novo em `AppState` precisa de default em `getDefaultState`; toda mudança de estado passa por action no reducer de `src/context/AppContext.tsx`.
- `Client` ganha `email` e status de descadastro; `Column` ganha marcação de pública.
- Fase 1: o navegador envia só eventos de movimento (com id para idempotência); o servidor aplica a Janela de espera e o agrupamento; Desfazer é um evento de cancelamento; o app consulta o serviço ao abrir e a cada 5 min.
- Candidato de infraestrutura: Supabase (edge function, tabela de eventos, fila agendada) e provedor transacional (Resend ou Postmark) com domínio próprio. **Pendente de decisão do arquiteto** (pergunta 1 do PRD).
- Fase 0: `mailto:` com assunto e corpo curtos; contador de uso em `AppState`.
- Textos novos em en e pt em `src/context/LanguageContext.tsx`.
- Lógica nova testada com vitest em `src/**/*.test.ts(x)`.

### Requisitos de UX

Não há documento de UX. Os comportamentos de interface vêm das jornadas UJ-1 a UJ-4 do PRD.

### Mapa de cobertura dos FRs

FR1: Épico 1, e-mail no cadastro do Cliente
FR2: Épico 1, pedir o e-mail no movimento
FR3: Épico 1, marcar coluna como pública
FR4: Épico 1, ativação com colunas sugeridas
FR5: Épico 2, gerar Aviso pendente
FR6: Épico 2, desfazer por Cartão
FR7: Épico 2, Janela de espera e agrupamento
FR8: Épico 2, conteúdo do Aviso
FR9: Épico 2, sem Aviso duplicado
FR10: Épico 2, Registro de envio no Cartão
FR11: Épico 2, Descadastro
FR12: Épico 2, Conta de envio
FR13: Épico 1, abrir e-mail já preenchido
FR14: Épico 1, contador de uso local

## Lista de épicos

### Épico 1: O freelancer avisa o cliente pelo próprio e-mail (Fase 0)
O Freelancer cadastra o e-mail dos Clientes, escolhe quais colunas são públicas e, ao mover um Cartão para uma delas, abre o próprio programa de e-mail com o aviso pronto. Tudo no navegador, sem conta e sem servidor. Valida se freelancers realmente querem avisar clientes (SM-1).
**FRs cobertos:** FR1, FR2, FR3, FR4, FR13, FR14
**NFRs:** NFR3

### Épico 2: O cliente recebe o aviso automaticamente (Fase 1)
O Freelancer cria uma Conta de envio e, a partir daí, o próprio FreelanceFlow envia o Aviso ao Cliente, juntando movimentos próximos, permitindo desfazer, mostrando o Registro de envio no Cartão e respeitando o Descadastro. Usa o e-mail do Cliente e as Colunas públicas do Épico 1.
**Só começa quando:** SM-1 for atingida na Fase 0 e o arquiteto responder a pergunta 1 do PRD (como o app ganha um serviço de envio).
**FRs cobertos:** FR5, FR6, FR7, FR8, FR9, FR10, FR11, FR12
**NFRs:** NFR1, NFR2, NFR3, NFR4, NFR5

## Épico 1: O freelancer avisa o cliente pelo próprio e-mail (Fase 0)

O Freelancer cadastra o e-mail dos Clientes, escolhe quais colunas são públicas e, ao mover um Cartão para uma delas, abre o próprio programa de e-mail com o aviso pronto. Tudo no navegador, sem conta e sem servidor.

### Story 1.1: Cadastrar o e-mail do cliente

Como freelancer,
Quero guardar o e-mail de cada cliente no cadastro dele,
Para que o app saiba para quem mandar o aviso.

**Critérios de aceite:**

**Dado** o formulário de cadastro ou edição de Cliente
**Quando** o Freelancer informa um e-mail válido e salva
**Então** o e-mail fica guardado no Cliente e aparece ao reabrir o cadastro
**E** a mudança passa por action no reducer e persiste via `saveState`

**Dado** um e-mail sem formato válido (por exemplo, "joao@")
**Quando** o Freelancer tenta salvar
**Então** o app não salva e mostra uma mensagem explicando o erro

**Dado** dados salvos antes desta mudança, com Clientes sem e-mail
**Quando** o app carrega
**Então** todos os Clientes aparecem normalmente, com o campo de e-mail vazio

**Dado** um Cliente com e-mail
**Quando** o Freelancer apaga o campo e salva
**Então** o Cliente fica sem e-mail

*Cobre FR1. Textos em en e pt.*

### Story 1.2: Marcar colunas como públicas

Como freelancer,
Quero marcar quais colunas do quadro são públicas,
Para que só os movimentos que interessam ao cliente gerem aviso.

**Critérios de aceite:**

**Dado** qualquer coluna do quadro
**Quando** o Freelancer usa a opção "Coluna pública" no menu da coluna
**Então** a coluna alterna entre pública e não pública
**E** colunas públicas mostram um indicador visual no cabeçalho

**Dado** uma Coluna pública
**Quando** o Freelancer renomeia a coluna
**Então** ela continua pública

**Dado** uma coluna recém-criada
**Quando** ela aparece no quadro
**Então** ela é não pública

**Dado** dados salvos antes desta mudança
**Quando** o app carrega
**Então** todas as colunas aparecem como não públicas

*Cobre FR3.*

### Story 1.3: Ativar o aviso ao cliente

Como freelancer,
Quero ligar o aviso ao cliente nas configurações com as colunas já sugeridas,
Para que eu comece a usar sem precisar configurar tudo.

**Critérios de aceite:**

**Dado** o aviso desligado
**Quando** o Freelancer liga "Avisar clientes por e-mail" nas configurações
**Então** o app sugere como públicas as colunas chamadas "Review", "Done", "Em revisão" ou "Concluído" (sem diferenciar maiúsculas)
**E** nenhuma coluna vira pública antes de o Freelancer confirmar

**Dado** um quadro sem colunas com esses nomes
**Quando** o Freelancer liga o aviso
**Então** o app pede para escolher as colunas públicas

**Dado** o aviso ligado
**Quando** o Freelancer desliga
**Então** nenhum aviso é oferecido e a marcação das colunas é mantida para uma próxima ativação

*Cobre FR4.*

### Story 1.4: Oferecer o aviso ao mover o cartão

Como freelancer,
Quero que, ao mover um cartão para uma coluna pública, o app abra meu e-mail com o aviso pronto,
Para que eu avise o cliente em um clique.

**Critérios de aceite:**

**Dado** o aviso ligado e um Cartão de Cliente com e-mail
**Quando** o Freelancer move o Cartão para uma Coluna pública
**Então** aparece a oferta "Avisar {Cliente} por e-mail"
**E** ao clicar, abre um link `mailto:` com destinatário, assunto e corpo preenchidos com o título do Cartão e a coluna

**Dado** o mesmo cenário
**Quando** o Freelancer ignora a oferta
**Então** nada acontece e a oferta some sozinha

**Dado** um Cartão sem Cliente, um movimento entre colunas não públicas ou uma reordenação na mesma coluna
**Quando** o movimento acontece
**Então** nenhuma oferta aparece

**Dado** qualquer uso desta função
**Quando** ela roda
**Então** nenhum dado sai do navegador por conta do app

*Cobre FR13. A montagem do texto e do link fica numa função pura com testes em vitest.*

### Story 1.5: Pedir o e-mail do cliente na hora do movimento

Como freelancer,
Quero que o app peça o e-mail do cliente quando ele faltar,
Para que eu não precise sair do quadro para cadastrar.

**Critérios de aceite:**

**Dado** o aviso ligado e um Cartão de Cliente sem e-mail
**Quando** o Freelancer move o Cartão para uma Coluna pública
**Então** o app pede o e-mail do Cliente ali mesmo, com as opções "Salvar" e "Pular"

**Dado** o pedido aberto
**Quando** o Freelancer informa um e-mail válido e salva
**Então** o e-mail fica no Cliente e a oferta da Story 1.4 aparece em seguida

**Dado** o pedido aberto
**Quando** o Freelancer clica em "Pular"
**Então** nenhuma oferta aparece
**E** o app não pede de novo o e-mail desse Cliente até recarregar a página

*Cobre FR2.*

### Story 1.6: Contar quantas vezes o aviso foi usado

Como dono do produto,
Quero que cada freelancer de teste veja quantas vezes por semana usou o aviso,
Para que eu consiga medir a Fase 0 sem telemetria.

**Critérios de aceite:**

**Dado** o aviso ligado
**Quando** o Freelancer clica em "Avisar {Cliente} por e-mail"
**Então** o contador da semana corrente aumenta em 1

**Dado** a tela de configurações
**Quando** o Freelancer abre
**Então** vê o uso desta semana e das 3 semanas anteriores

**Dado** dados salvos antes desta mudança
**Quando** o app carrega
**Então** o contador começa em zero (default em `getDefaultState`)

*Cobre FR14 e permite medir SM-1.*

## Épico 2: O cliente recebe o aviso automaticamente (Fase 1)

O Freelancer cria uma Conta de envio e o FreelanceFlow passa a enviar o Aviso sozinho, juntando movimentos próximos, permitindo desfazer, mostrando o Registro de envio e respeitando o Descadastro.

> **Bloqueado até:** SM-1 atingida na Fase 0 e decisão do arquiteto sobre o serviço de envio (pergunta 1 do PRD). Os critérios abaixo descrevem comportamento; a implementação segue o documento de arquitetura quando ele existir.
> **Liberação:** o envio para Clientes reais só é ligado depois da Story 2.5 (Descadastro).

### Story 2.1: Criar a conta de envio

Como freelancer,
Quero criar uma conta mínima com meu nome e e-mail,
Para que o FreelanceFlow possa enviar avisos em meu nome.

**Critérios de aceite:**

**Dado** o aviso ligado e sem Conta de envio
**Quando** o Freelancer escolhe "Enviar automaticamente" e informa nome e e-mail
**Então** recebe um link de acesso no e-mail informado

**Dado** o link recebido
**Quando** o Freelancer abre o link
**Então** a Conta de envio fica confirmada e o app mostra "Envio automático ativo"
**E** a oferta da Story 1.4 deixa de aparecer para esse Freelancer

**Dado** a conta sem confirmação
**Quando** o Freelancer move Cartões
**Então** nenhum aviso automático é enviado e a Fase 0 continua funcionando

**Dado** a conta confirmada
**Quando** o Freelancer desliga o envio automático
**Então** nenhum aviso futuro é enviado e o resto do app segue igual, sem exigir internet (NFR3)

*Cobre FR12.*

### Story 2.2: Enviar o aviso ao cliente

Como freelancer,
Quero que o cliente receba um e-mail quando um cartão dele entra numa coluna pública,
Para que eu não precise avisar manualmente.

**Critérios de aceite:**

**Dado** Conta de envio confirmada e um Cartão de Cliente com e-mail
**Quando** o Freelancer move o Cartão para uma Coluna pública
**Então** o app envia ao serviço um evento de movimento com id único
**E** o evento contém só título do Cartão, nome da coluna, nome e e-mail do Cliente, nome e e-mail do Freelancer e id (NFR2)

**Dado** o evento recebido
**Quando** passam 2 minutos sem outro movimento desse Cliente
**Então** o Cliente recebe um e-mail com o nome do Freelancer, o título do Cartão e a coluna, no idioma do app
**E** a resposta ao e-mail vai para o e-mail do Freelancer
**E** o e-mail não mostra horas, valores nem descrição

**Dado** o mesmo evento enviado duas vezes (por exemplo, app aberto em dois navegadores)
**Quando** o serviço processa
**Então** só um e-mail sai (FR9)

**Dado** a configuração do domínio de envio
**Quando** um aviso é enviado
**Então** ele sai com SPF e DKIM válidos (NFR4)

*Cobre FR5, FR8, FR9, NFR2, NFR4.*

### Story 2.3: Desfazer e juntar movimentos num aviso só

Como freelancer,
Quero desfazer um movimento antes do e-mail sair e que movimentos próximos virem um e-mail só,
Para que o cliente não receba avisos errados nem uma enxurrada de e-mails.

**Critérios de aceite:**

**Dado** um movimento que gerou aviso
**Quando** o movimento acontece
**Então** o Freelancer vê "Aviso para {Cliente} sai em 2 min. Desfazer"

**Dado** um aviso com só um Cartão
**Quando** o Freelancer clica em "Desfazer"
**Então** o aviso é cancelado e nenhum e-mail sai

**Dado** três Cartões do mesmo Cliente movidos com 1 minuto de intervalo
**Quando** passam 2 minutos sem novo movimento
**Então** sai um único e-mail com os três Cartões, cada um na coluna final

**Dado** um aviso com três Cartões
**Quando** o Freelancer desfaz um deles
**Então** o e-mail sai só com os outros dois

**Dado** um Cartão que volta para coluna não pública dentro da janela
**Quando** a janela termina
**Então** ele não aparece no e-mail

**Dado** movimentos contínuos do mesmo Cliente
**Quando** completam 10 minutos desde o primeiro
**Então** o e-mail sai mesmo assim, em até 2 minutos (NFR1)

**Dado** a aba fechada durante a janela
**Quando** a janela termina
**Então** o e-mail sai normalmente

*Cobre FR6, FR7, NFR1. O serviço registra cada Desfazer para medir SM-C3.*

### Story 2.4: Mostrar o registro de envio no cartão

Como freelancer,
Quero ver no cartão se o aviso foi enviado, cancelado ou falhou,
Para que eu saiba se o cliente foi avisado.

**Critérios de aceite:**

**Dado** um Cartão com aviso
**Quando** o Freelancer olha o Cartão
**Então** vê o estado do último aviso: pendente, enviado (com horário), cancelado ou falhou

**Dado** um aviso que falhou
**Quando** o Freelancer olha o Cartão
**Então** vê o motivo em linguagem simples, por exemplo "e-mail do cliente inválido"

**Dado** o app aberto
**Quando** ele carrega e depois a cada 5 minutos
**Então** busca no serviço o estado atualizado dos avisos

**Dado** o app sem internet
**Quando** o Freelancer olha o Cartão
**Então** vê o último estado conhecido e a hora da última atualização

*Cobre FR10.*

### Story 2.5: Deixar o cliente parar de receber avisos

Como cliente de um freelancer,
Quero parar de receber avisos com um clique,
Para que eu controle o que chega na minha caixa de entrada.

**Critérios de aceite:**

**Dado** um aviso recebido
**Quando** o Cliente clica em "Não quero mais receber avisos de {Freelancer}" no rodapé
**Então** vê uma página confirmando o pedido, sem precisar de login
**E** nenhum aviso sai para ele a partir de 1 minuto depois (NFR5)

**Dado** um Cliente que fez Descadastro
**Quando** o app do Freelancer atualiza os estados
**Então** o cadastro do Cliente mostra "pediu para não receber avisos"
**E** o Freelancer não tem opção para reativar sozinho

**Dado** um Cliente com e-mail
**Quando** o Freelancer apaga o e-mail no app
**Então** o serviço também apaga o e-mail desse Cliente (NFR5)

*Cobre FR11, NFR5. Depois desta história, o envio pode ser ligado para Clientes reais.*
