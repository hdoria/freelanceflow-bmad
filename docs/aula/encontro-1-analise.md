# Encontro 1: do problema ao brief

Fase de análise do BMad, aplicada a uma feature nova do FreelanceFlow. O encontro termina com um brief aprovado pelo PO (gate 1). O PRD fica para o encontro 2.

Quem conduz a conversa com os agentes é o PO. O resto do time acompanha na tela e anota as perguntas que o agente fez e que ninguém sabia responder: elas são o material mais útil do encontro.

## Antes de começar (10 minutos)

```sh
git clone https://github.com/hdoria/freelanceflow-bmad.git
cd freelanceflow-bmad
npm ci
npm run dev
```

1. Confira o app em `http://localhost:8080`: crie uma tarefa, ligue o cronômetro, mude a tarefa de coluna.
2. Confira `node --version` (20.12 ou mais novo) e `uv --version`.
3. Abra o Claude Code na raiz do repositório e rode `/bmad-help`. A resposta lista as skills instaladas.

## O ponto de partida

O FreelanceFlow registra tarefas, tempo e valor por hora. Duas coisas chamam atenção ao usar:

- Os clientes estão fixos no código, Acme Corp e TechStart. Não dá para cadastrar cliente novo.
- A receita aparece só como total no topo. Não existe visão por cliente nem do que ainda falta cobrar.

Essas duas observações são o ponto de partida, não a solução. O brainstorm decide qual problema vale resolver primeiro.

## Passo a passo (40 minutos)

Cada passo começa numa sessão nova do Claude Code e termina com commit.

### 1. Brainstorm com a Mary (15 minutos)

```text
/bmad-brainstorming
```

Cole este pedido quando ela perguntar o tema:

```text
Quero explorar uma feature nova pro FreelanceFlow. Hoje o freelancer registra tarefas e tempo, mas os clientes são fixos no código e não existe nenhuma visão de quanto ele tem a receber de cada cliente. Quem usa é um freelancer que cobra por hora e atende vários clientes ao mesmo tempo. O que eu quero no fim é entender qual problema de dinheiro e de cliente vale resolver primeiro. Ainda não decidi a solução.
```

Deixe a Mary escolher as técnicas. Responda como o freelancer, com situações concretas: o fim do mês, o cliente que atrasa, a tarefa que não foi cobrada.

Saída: `_bmad-output/brainstorming/brainstorm-<tema>-<data>/`.

### 2. Atacar a ideia (10 minutos)

Escolha a direção mais forte do brainstorm e rode:

```text
/bmad-forge-idea
```

Personas diferentes atacam os pontos fracos até a ideia seguir, morrer ou ficar clara. Se faltar fato de fora (como outros apps de freelancer resolvem cobrança, por exemplo), use `/bmad-deep-recon` no lugar.

### 3. Brief no caminho rápido (10 minutos)

```text
/bmad-product-brief
```

Quando ela perguntar o modo de trabalho, escolha o caminho rápido (fast path). O agente agrupa as lacunas em uma ou duas perguntas, escreve o brief inteiro e marca com `[ASSUMPTION]` tudo o que supôs.

Saída: `_bmad-output/planning-artifacts/briefs/brief-freelanceflow-bmad-<data>/brief.md`.

### 4. Gate 1 (5 minutos)

O PO lê o brief e responde:

1. O problema está escrito do ponto de vista de quem sofre?
2. Quem usa a primeira versão tem uma persona concreta?
3. O critério de sucesso tem número?
4. O escopo diz o que fica de fora?
5. Toda suposição marcada com `[ASSUMPTION]` foi confirmada ou cortada?

Resposta fraca volta para a Mary. Com as 5 respondidas, o PO aprova e o brief vira a entrada do PRD no encontro 2.

## O que não fazer hoje

- Falar de banco de dados, API ou framework no brief. A análise decide o quê e o porquê; o como é do arquiteto, no encontro 2.
- Pedir código. Nenhum arquivo de `src/` muda neste encontro.
- Encadear os três workflows na mesma sessão. O contexto de um contamina o outro.
