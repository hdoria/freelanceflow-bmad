# FreelanceFlow

Kanban de tarefas e tempo para freelancers: tarefas por coluna, cronômetro por tarefa, cliente, valor por hora e receita estimada. React, Vite, TypeScript, shadcn/ui e Tailwind. Não tem backend: os dados ficam no `localStorage` do navegador.

O repositório serve de base para praticar spec-driven development com o [BMad Method](https://docs.bmad-method.org/), já instalado e configurado.

## Rodar o app

Pré-requisitos: Node 20.12 ou mais novo e Git.

```sh
git clone https://github.com/hdoria/freelanceflow-bmad.git
cd freelanceflow-bmad
npm ci
npm run dev
```

O app abre em `http://localhost:8080`. Outros comandos: `npm run build`, `npm test` e `npm run lint`. O lint já sai com 3 erros herdados da base; o `AGENTS.md` explica.

## O BMad neste repositório

| Item | Estado |
| --- | --- |
| Versão | BMad Method 6.12.4, a estável mais recente em 23/11/2026 |
| Módulos | Core e BMad Method (BMM) |
| Clientes de IA | Claude Code (`.claude/skills`) e Codex (`.agents/skills`) |
| Idioma | Português do Brasil, na conversa e nos documentos (`_bmad/custom/config.toml`) |
| Contexto do projeto | Bloco gerado pelo `bmad-project-context` no `AGENTS.md` |
| Saída dos workflows | `_bmad-output/` |

Pré-requisito extra do BMad: [uv](https://docs.astral.sh/uv/), que as skills usam para rodar scripts.

Para começar, abra o cliente de IA na raiz do repositório e chame a ajuda:

```text
claude
> /bmad-help
```

No Codex, o prefixo é `$`, como em `$bmad-help`.

Seu nome nos agentes fica em `_bmad/custom/config.user.toml`, que não vai pro Git:

```toml
[core]
user_name = "Seu nome"
```

## Prática

- [Encontro 1, fase de análise](docs/aula/encontro-1-analise.md): do problema ao brief de uma feature nova, com brainstorm, teste da ideia e product brief.

## Hábitos ao usar o BMad aqui

1. Abra uma sessão nova do cliente de IA para cada workflow.
2. Faça commit no fim de cada workflow, junto com o que ele gravou em `_bmad-output/`.
3. Ajuste do time vai em `_bmad/custom/`. A pasta `_bmad/` é da instalação e é sobrescrita na próxima atualização.
