<!-- bmad:context -->
<!-- Verified 2026-11-23 against 75148cc. Managed by bmad-project-context; edits inside this block are replaced on refresh. Keep anything you want preserved outside the markers. -->

## FreelanceFlow

Kanban de tarefas e tempo para freelancers. React 18, Vite, TypeScript, shadcn/ui, Tailwind. Sem backend: todos os dados ficam em localStorage. Artefatos de planejamento BMad vão em `_bmad-output/`.

## Where things are

- Estado e regras de negócio (reducer, receita, timer): `src/context/AppContext.tsx`
- Persistência e dados de exemplo: `src/lib/storage.ts`; tipos em `src/types/index.ts`
- Textos en/pt: `src/context/LanguageContext.tsx`
- `src/components/ui/` é shadcn; adicione componentes com o CLI (`components.json`), não escreva à mão.

## Running and verifying

- `npm run lint` já sai com 3 erros e vários warnings (`ui/command.tsx`, `ui/textarea.tsx`, `tailwind.config.ts`); compare com essa linha de base, não trate como regressão sua.
- `npm test` só tem um teste placeholder (`src/test/example.test.ts`); ele passar não prova nada. Teste lógica nova com vitest em `src/**/*.test.ts(x)`.
- `npm run build` termina ok, com aviso de chunk > 500 kB; o aviso é esperado.

## Conventions that differ from defaults

- Persistência só via `saveState`/`loadState` (chave `freelancer-kanban-data`); idioma usa a chave separada `app-language`. Nunca chame `localStorage` direto para os dados do app.
- Mudança de estado passa por action no reducer de `AppContext.tsx`; não mute `state` nem escreva em storage fora do efeito do provider.
- Tempo em segundos (`timeSpent`, `timeEstimate`), datas em ms (`Date.now()`). `timeSpent` não inclui o timer em andamento: ele só soma em `STOP_TIMER`/`START_TIMER`.
- Campo novo em `AppState`: dê default em `getDefaultState`, pois `loadState` faz merge raso do salvo sobre o default.

<!-- /bmad:context -->
