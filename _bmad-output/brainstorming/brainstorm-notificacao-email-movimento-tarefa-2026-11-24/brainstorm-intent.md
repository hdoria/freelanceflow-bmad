---
source: .memlog.md
date: 2026-11-24
topic: Notificação por e-mail quando uma tarefa muda de coluna no kanban
---

# Intenção: notificação por e-mail no movimento de tarefa

> Sessão de demonstração: as respostas do usuário após a Folha 3 foram assumidas pela facilitadora.

## Problema e objetivo

- Pedido: avisar por e-mail quando um cartão muda de coluna.
- Insight central: o pedido esconde três produtos (aviso, aprovação, cobrança). O valor está no ciclo revisão > aprovação > fatura; o e-mail transforma o kanban em ferramenta de cobrança.
- Objetivo do MVP: entregar o aviso ao cliente, com desenho que já preveja o ciclo de aprovação.

## Usuários

- Freelancer: configura o que notifica, controla ruído, acompanha envios; depois recebe resumos e fatura mais rápido.
- Cliente do freelancer: recebe avisos de entregas; depois aprova ou pede ajuste sem login.

## Escopo do MVP (MUST)

1. Campo e-mail no cliente; pedido no primeiro movimento que notificaria, se vazio.
2. Colunas marcadas como públicas; padrão pronto: só "Em revisão" e "Concluído", ativado com um clique.
3. E-mail ao cliente quando o cartão entra numa coluna pública.
4. Anti-ruído: "e-mail sai em 60s, desfazer" e agrupamento de movimentos em janela de 10 min num único e-mail.
5. Link de descadastro no rodapé, com registro de opt-out.
6. Registro de envio no cartão (ex.: "enviado 14:02").

## Depois

- SHOULD: botões Aprovar / Pedir ajuste com token de uso único; ajuste volta o cartão para "Em andamento" com o comentário; resumo diário para o freelancer (só quando há algo, item mais urgente no assunto).
- COULD: fatura em rascunho na aprovação com horas x valor/hora; contador de rodadas de ajuste (aviso de rodada cobrável); resumo semanal com meta de faturamento; página de status do projeto só-leitura.

## Fora (WON'T agora)

Pix, WhatsApp, entidade "projeto", anexos do entregável, relatório mensal para nota fiscal.

## Sequência em fases

- Fase 0, mailto: sem backend. Ao mover para coluna pública, abre `mailto:` já preenchido. Objetivo: validar se há uso real.
- Fase 1, backend mínimo (ex.: Supabase edge function + fila) entregando o MUST.
- Fase 2, ciclo de aprovação e fatura (SHOULD/COULD de aprovação e dinheiro).

## Restrições e riscos

- Sem backend hoje: tudo roda no navegador; e-mail com navegador fechado exige servidor. Quebra a premissa atual do app.
- Dados em localStorage (`saveState`/`loadState`): o servidor não conhece os cartões. Mitigação: enviar ao backend só o evento de movimento.
- `Client` não tem campo de e-mail (hoje: id, name, hourlyRate, color). Campo novo exige default em `getDefaultState` e action no reducer.
- Não há entidade projeto: `Task` aponta só para `clientId`. Agrupar por projeto exigiria criar o conceito ou usar tags.
- Spam: serviço transacional com domínio verificado (SPF/DKIM), reply-to do freelancer.
- LGPD: e-mail de terceiro guardado; exige descadastro e registro de opt-out.
- Token de aprovação (Fase 2): uso único, com validade, registrando quem clicou; evita aprovação por link encaminhado.
- Loop de notificação: movimento originado pelo cliente não notifica o cliente.
- Idempotência: id do evento para evitar e-mail duplicado entre dispositivos com estado divergente.
- Exposição de valor: chave por cliente para mostrar ou esconder horas e valor; tom focado na entrega.
- Fatura automática com valor errado (timer esquecido): fatura nasce como rascunho.
- Outros: janela de envio em horário comercial; template pt/en editável conforme idioma do cliente; custo de envio limita plano grátis.

## Decisão em aberto para arquitetura

Como o app ganha backend sem perder a simplicidade do localStorage: só eventos de movimento vão ao servidor, ou estado sincronizado? Qual provedor (Supabase sugerido) e qual serviço de e-mail transacional? Define o que é viável a partir da Fase 1.

## Perguntas em aberto

- Destinatário principal: foi assumido o cliente (pedido original disse "ele", ambíguo entre cliente e o próprio freelancer).
- A Fase 0 (mailto) é suficiente para validar demanda, e qual o critério de sucesso para seguir à Fase 1?
- Configuração por cliente (cliente A recebe tudo, B só entregas) entra no MVP ou depois?
- Limite de envios do plano grátis: qual valor?
- Base legal LGPD para guardar e-mail do cliente: consentimento ou legítimo interesse?
