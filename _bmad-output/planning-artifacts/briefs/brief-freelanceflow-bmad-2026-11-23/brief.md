---
title: "Product Brief: FreelanceFlow, carteira por cliente"
status: draft
created: 2026-11-23
updated: 2026-11-23
---

# Product Brief: FreelanceFlow, carteira por cliente

## Resumo executivo

FreelanceFlow é um kanban de tarefas e tempo para freelancers, web, sem backend, com todos os dados em localStorage. Hoje ele registra horas e mostra uma receita única que só cresce. Esta brief propõe a primeira aposta de dinheiro: uma carteira por cliente, com cadastro de clientes na interface e o total de cada um em três estados: a cobrar, cobrado e recebido.

O motivo é simples. O freelancer que cobra por hora e atende vários clientes guarda o dinheiro em planilha, conversa e memória, longe do registro de horas. Ele não sabe quanto tem a receber, de quem, e esquece de cobrar porque a cobrança segue o calendário do cliente. A necessidade de fundo é sentir que o trabalho feito virou dinheiro garantido, sem depender de lembrar sozinho.

## O problema

- O app mistura trabalhado, cobrado e recebido em um número só, calculado como `timeSpent` x taxa e somado para sempre. Nada diminui quando o cliente paga.
- O cliente não existe de verdade no produto: o modelo e o reducer têm ADD, UPDATE e DELETE de cliente, mas nenhum componente os despacha. Os clientes vêm de um seed fixo.
- Horas sem cliente valem R$ 0 silenciosamente, escondendo trabalho sem dono.
- Quem atende vários clientes cobra por planilha ou memória, e a cobrança vai para "semana que vem".

## A solução

1. **Cadastro de clientes na UI.** Criar, editar e arquivar. Cadastro mínimo: nome e taxa por hora. Arquivar em vez de apagar, para preservar o histórico financeiro.
2. **Carteira por cliente.** Para cada cliente, o total em três estados: a cobrar, cobrado e recebido.
3. **Status de cobrança próprio.** O freelancer marca horas como cobradas e depois recebidas, à mão. O status é independente da coluna do quadro: mover uma tarefa para Backlog não reabre o dinheiro.
4. **Horas sem cliente visíveis.** Aparecem como alerta, não como R$ 0.
5. **Valor cobrado imutável.** Mudar a taxa do cliente não altera o valor de horas já cobradas.

## O que torna isto diferente

Não há moat técnico. A vantagem é de foco: o dinheiro vive junto do registro de horas, no mesmo app, sem integração. Para o freelancer solo, isso substitui a planilha paralela. A escolha de ficar sem backend mantém o produto simples e privado, ao custo de limites descritos em Escopo.

## Quem isto atende

Freelancer que cobra por hora e atende vários clientes ao mesmo tempo, sozinho, sem equipe financeira. [ASSUMPTION] Usa um único navegador em um único dispositivo. [ASSUMPTION] Aceita registrar cobranças e pagamentos manualmente, pois não há integração bancária nem de fatura. Esta é a pergunta aberta 3 do intent e precisa ser validada.

## Critérios de sucesso

Observáveis, sem metas numéricas (herdados do intent):

- Dá para cadastrar, editar e arquivar clientes pela UI.
- O freelancer vê, por cliente, a cobrar, cobrado e recebido, em vez de um número único.
- Marcar horas como cobradas e depois recebidas altera os totais sem depender da coluna do quadro.
- Horas sem cliente aparecem de forma visível, não como R$ 0.
- Mudar a taxa do cliente não altera o valor de horas já cobradas.
- Arquivar um cliente preserva seu histórico financeiro.

## Escopo

**Dentro (primeira versão):** cadastro, edição e arquivamento de clientes; totais por cliente nos três estados; marcação manual de cobrado e recebido; alerta de horas sem cliente; valor cobrado congelado.

**Fora por ora:** nota fiscal, envio de e-mail, multi-moeda, integração bancária, sincronização entre dispositivos, cobrança por unidade que não seja hora, fatura formal. Também ficam fora período, vencimento e atrasado, que dependem de gravar `timeEntries` por sessão do timer.

**Restrições técnicas relevantes** (de `AGENTS.md` e do intent):
- Persistência só via `saveState`/`loadState`; estado muda só por action no reducer; campo novo em `AppState` exige default em `getDefaultState`.
- `timeSpent` não inclui o timer em andamento, então o "a cobrar" fica abaixo da realidade com timer ativo. [ASSUMPTION] Aceitável na primeira versão, desde que isso seja sinalizado na UI.
- `DELETE_CLIENT` hoje zera `clientId` das tarefas e orfana o dinheiro; arquivar substitui esse caminho para clientes com histórico.
- [ASSUMPTION] Marcar como cobrado exige um registro com valor congelado (horas e taxa no momento da cobrança), pois o modelo atual não guarda isso. O desenho exato fica para o PRD.

## Perguntas em aberto

1. Como o freelancer define "cobrado": hora registrada, entregue (Done) ou aprovada pelo cliente? [ASSUMPTION] Na primeira versão, é uma marcação manual do freelancer, sem regra automática.
2. O ciclo de cobrança varia por cliente (mês fechado, projeto, marco, hora avulsa)? [ASSUMPTION] Ignorado na primeira versão: a marcação é por conjunto de horas escolhido pelo freelancer.
3. Ele aceita registrar o pagamento à mão? [ASSUMPTION] Sim; validar com usuários.
4. Perder os dados do navegador é aceitável, ou exportar e importar é requisito? [ASSUMPTION] Com dinheiro no app, exportar e importar provavelmente vira requisito; decidir antes do PRD.

## Visão

A segunda aposta é o "fechamento do mês" por cliente: congela taxas e horas em um fechamento imutável e gera texto de cobrança pronto para WhatsApp ou e-mail. Depende desta carteira e de gravar `timeEntries` por sessão. Depois vêm previsão e risco, concentração de receita por cliente e vencimento por prazo de pagamento. Se funcionar, o FreelanceFlow deixa de ser um cronômetro com kanban e vira o lugar onde o freelancer sabe, a qualquer momento, quanto lhe devem e quem deve.
