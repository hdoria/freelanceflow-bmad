# Intenção: dinheiro a receber por cliente no FreelanceFlow

Origem: brainstorm de 2026-11-23. Insumo para bmad-product-brief, bmad-prd ou bmad-spec.

## 1. Problema e para quem

Para o freelancer que cobra por hora e atende vários clientes. Hoje o dinheiro vive em planilha, conversa e memória, longe do registro de horas. Ele não sabe quanto tem a receber, de quem, e esquece de cobrar porque cobrar segue o calendário do cliente (fechamento, NF), não o dele. Juntar horas, descrever e calcular dá trabalho e a cobrança vai para "semana que vem".

Necessidade de fundo: sentir que o trabalho feito virou dinheiro garantido, sem ter de lembrar sozinho.

Problema de dinheiro mais valioso e barato: separar trabalhado, cobrado e recebido, por cliente. O app mistura os três num número só.

## 2. Fatos do código que condicionam a solução

- ADD_CLIENT, UPDATE_CLIENT e DELETE_CLIENT existem no reducer, mas nenhum componente os despacha. O cliente é fixo por falta de UI, não de modelo. Só o Header usa `getTotalRevenue`.
- `TimeEntry` existe nos tipos, mas `timeEntries` nunca é gravado (só `timeSpent` acumulado). Sem data de cada trabalho, não há "a receber em novembro" nem "atrasado".
- Receita = `timeSpent` x taxa, somada para sempre. Não há estado cobrado, pago ou vencido; o total nunca diminui.
- `timeSpent` não inclui o timer em andamento, então o a receber fica abaixo da realidade com timer ativo.
- DELETE_CLIENT zera `clientId` das tarefas e orfana o dinheiro.
- `getTaskRate` devolve 0 sem cliente, o que esconde trabalho sem dono.
- Taxa alterada reescreve o passado; cobrança passada precisa ser imutável.
- Sem backend: sem e-mail de fatura; a saída é texto ou PDF. `loadState` faz merge raso, então todo campo novo em `AppState` exige default em `getDefaultState`.

## 3. Direção recomendada: Carteira por cliente (primeira aposta)

Cadastro de clientes na UI + total por cliente em três estados: a cobrar, cobrado, recebido. Só localStorage, sem fatura formal.

Por quê:
- O cliente não existe de verdade no produto (seed fixo, sem UI); sem isso, quem atende vários clientes não usa feature de dinheiro nenhuma.
- Valor alto e esforço baixo: o cadastro destrava tudo, e o status de cobrança responde ao goal.
- Dependência encadeada: cliente real antes de qualquer visão de dinheiro; sem status não há "não cobrado".
- Cadastro mínimo: nome e taxa; o resto aparece quando faz falta.
- Status de cobrança independente da coluna do quadro (mover para Backlog não reabre o dinheiro).
- Arquivar cliente em vez de apagar, mantendo o histórico financeiro.

## 4. Segunda aposta e dependências

"Fechamento do mês" por cliente: congela taxas e horas num fechamento imutável e gera texto de cobrança pronto (WhatsApp ou e-mail). Depende da primeira aposta e de gravar `timeEntries` por sessão do timer, que também destrava período e vencimento.

Fica para depois, dependente das duas: previsão e risco (esforço alto), concentração de receita por cliente, vencimento por prazo de pagamento.

## 5. Perguntas a validar antes de construir

1. Como o freelancer define "cobrado": hora registrada, entregue (Done) ou aprovada pelo cliente?
2. O ciclo de cobrança varia por cliente (mês fechado, projeto, marco, hora avulsa)?
3. Ele aceita registrar o pagamento à mão?
4. Perder os dados do navegador (localStorage) é aceitável, ou exportar e importar é requisito?

## 6. Fora de escopo por ora

Nota fiscal, envio de e-mail, multi-moeda, integração bancária e sincronização entre dispositivos (exigem backend ou decisão de produto à parte). Cobrança por unidade que não seja hora (por exemplo, por palavra) também fica fora.

## 7. Critérios de sucesso observáveis

Derivados das descobertas do log, sem metas numéricas:
- Dá para cadastrar, editar e arquivar clientes pela UI.
- O freelancer vê, por cliente, o total em três estados (a cobrar, cobrado, recebido), em vez de um número único.
- Marcar horas como cobradas e depois recebidas altera os totais sem depender da coluna do quadro.
- Horas sem cliente aparecem de forma visível, não como "R$ 0".
- Mudar a taxa do cliente não altera o valor de horas já cobradas.
- Arquivar um cliente preserva seu histórico financeiro.
