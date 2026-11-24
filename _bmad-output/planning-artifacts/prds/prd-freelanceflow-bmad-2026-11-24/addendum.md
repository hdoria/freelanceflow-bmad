# Addendum: Aviso ao cliente por e-mail

Material que não cabe no PRD, mas que arquitetura e UX vão precisar. Nada aqui é decisão final: são as hipóteses que saíram do brainstorming para o arquiteto confirmar ou trocar.

## Estado atual do código (24/11/2026)

- Todo o estado vive no navegador, via `saveState`/`loadState` em `src/lib/storage.ts` (chave `freelancer-kanban-data`). Não existe servidor.
- `Client` tem só `id`, `name`, `hourlyRate` e `color` (`src/types/index.ts`). Precisa ganhar `email` (FR-1) e um status de descadastro (FR-11).
- `Column` tem `id`, `title`, `order`. Precisa ganhar a marcação de coluna pública (FR-3). Como o padrão do FR-4 é por coluna, o app precisa saber quais colunas nasceram como "Review" e "Done"; hoje só o título e a ordem indicam isso.
- Campo novo em `AppState` precisa de default em `getDefaultState` (o `loadState` faz merge raso). Mudança de estado passa por action no reducer de `src/context/AppContext.tsx`.

## Hipótese de transporte (Fase 1)

- O navegador envia ao servidor só o **evento de movimento** (id do evento, id do cartão, título, coluna nova, cliente e e-mail), não o estado inteiro. Isso mantém o app funcionando sem conta para quem não ativa (NFR-3) e reduz dado fora do navegador (NFR-2).
- O id do evento garante idempotência (FR-9).
- Janela de desfazer: o mais simples é segurar o evento no navegador por 60s antes de enviar. Risco: se a aba fechar nesse intervalo, o aviso não sai. Alternativa: enviar na hora e o servidor esperar 60s antes de processar, com um evento de cancelamento. A segunda cobre o fechamento da aba.
- Agrupamento de 10 minutos acontece no servidor, por cliente.
- Candidato citado na sessão: Supabase (edge function, tabela de eventos e fila agendada), com provedor transacional de e-mail (Resend ou Postmark) e domínio próprio com SPF/DKIM.

## Fase 0 (mailto)

- `mailto:` com `subject` e `body` codificados abre o programa de e-mail padrão. Limite prático de tamanho de URL em alguns clientes de e-mail: manter o texto curto.
- Contador de uso local (FR-14) pode ficar em `AppState`, com default em `getDefaultState`.

## Riscos levantados no Reverse Brainstorming

| Falha | Como o PRD responde |
|---|---|
| E-mail a cada arrasto, inclusive os errados | FR-6 (desfazer), FR-7 (agrupamento) |
| Cair no spam | NFR-4 |
| Cliente recebe aviso de coluna interna | FR-3 (só colunas públicas) |
| Expor valor/hora | FR-8 (aviso sem horas e valores) |
| Freelancer não sabe se o e-mail saiu | FR-10 |
| Cliente sem e-mail cadastrado | FR-2 |
| LGPD | FR-11, NFR-5 |
| E-mail duplicado entre dispositivos | FR-9 |
| Ninguém configura | FR-4 |
| Loop de aviso causado pela resposta do cliente | Não se aplica no MVP; volta na Fase 2 com a aprovação pelo e-mail |
