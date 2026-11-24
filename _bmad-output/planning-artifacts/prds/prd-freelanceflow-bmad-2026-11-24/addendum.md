# Addendum: Aviso ao cliente por e-mail

Material que não cabe no PRD, mas que arquitetura e UX vão precisar. Nada aqui é decisão final: são as hipóteses que saíram do brainstorming para o arquiteto confirmar ou trocar.

## Estado atual do código (24/11/2026)

- Todo o estado vive no navegador, via `saveState`/`loadState` em `src/lib/storage.ts` (chave `freelancer-kanban-data`). Não existe servidor.
- `Client` tem só `id`, `name`, `hourlyRate` e `color` (`src/types/index.ts`). Precisa ganhar `email` (FR-1) e um status de descadastro (FR-11).
- `Column` tem `id`, `title`, `order`. Precisa ganhar a marcação de coluna pública (FR-3). O FR-4 sugere colunas pelo nome ("Review", "Done", "Em revisão", "Concluído") e pede confirmação, porque as colunas salvas não registram de onde vieram.
- Campo novo em `AppState` precisa de default em `getDefaultState` (o `loadState` faz merge raso). Mudança de estado passa por action no reducer de `src/context/AppContext.tsx`.

## Hipótese de transporte (Fase 1)

- O navegador envia ao servidor só o **evento de movimento** (id do evento, id do cartão, título, coluna nova, cliente e e-mail), não o estado inteiro. Isso mantém o app funcionando sem conta para quem não ativa (NFR-3) e reduz dado fora do navegador (NFR-2).
- O id do evento garante idempotência (FR-9).
- Janela de espera (recomendado depois da revisão do PRD): o navegador envia o evento na hora e o servidor espera 2 minutos sem novos movimentos do mesmo cliente (teto de 10) antes de enviar. Desfazer manda um evento de cancelamento daquele cartão. Assim fechar a aba não perde o aviso (FR-6) e o servidor consegue medir a taxa de Desfazer (SM-C3). A alternativa de segurar o evento no navegador foi descartada por esses dois motivos.
- Canal de volta (FR-10, FR-11): o app consulta o serviço ao abrir e a cada 5 minutos para buscar o Registro de envio dos cartões e o Descadastro dos clientes. Tempo real (websocket) não é necessário no MVP.
- O servidor guarda e-mail do cliente e status de descadastro por freelancer; apagar o e-mail no app manda um evento que apaga no servidor (NFR-5).
- Nome do freelancer: hoje o app não guarda; passa a ser pedido na Conta de envio (FR-12).
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
