---
name: daquele-jeito
description: "Workflow para projetos de execução não-triviais — plan-first com bateria de perguntas, progresso visível, auditoria em 4 eixos, loop de melhoria, calibrar esforço, autonomia em bugs, subagentes. ATIVAR APENAS quando: (1) usuário invoca via /daquele-jeito, OU (2) a frase 'faz daquele jeito' / 'faça daquele jeito' (case-insensitive) aparece como IMPERATIVO DE INVOCAÇÃO no prompt — tipicamente como instrução final/standalone, com sentido 'aplique este workflow ao pedido acima/abaixo'. Posição típica: nas últimas linhas após o usuário descrever o projeto, ou abrindo um novo pedido. ATIVA: 'Quero criar um SaaS de X. [descrição longa]. Faz daquele jeito.' (instrução final), 'Faça daquele jeito o setup do projeto', 'Faz daquele jeito: criar X'. NÃO ATIVA: 'faz daquele jeito alternativo que mencionei' (referência a método discutido), 'não faz daquele jeito' (negação), 'se fizer daquele jeito...' (condicional), 'você fez daquele jeito ontem' (passado), 'como funciona o faz daquele jeito?' (meta-pergunta). Regra prática: frase seguida por qualificadores (alternativo, anterior, que [verbo], daquele outro) ou precedida por negação/condicional/tempo passado = REFERÊNCIA, não invocação. EM DÚVIDA, NÃO ATIVE — usuário pode invocar explícito via /daquele-jeito."
---

# Daquele-jeito — workflow para projetos de execução

Aplique este workflow ao pedido do usuário e a todo trabalho subsequente nesta conversa. Aplica-se a tarefas estruturais — código, infra, conteúdo, design.

**Anúncio de ativação:** na **primeira vez** que este workflow é ativado em uma conversa, abra a resposta com uma frase curta no formato *"Vou fazer daquele jeito o [tipo de trabalho]..."* — ex.: *"Vou fazer daquele jeito o planejamento desse SaaS..."*, *"Vou fazer daquele jeito o fix desse bug..."*, *"Vou fazer daquele jeito a análise comparativa..."*. Em ativações seguintes na mesma conversa, **não repita** — usuário já sabe que está ativa.

## 1. Plan-first

Ativar este workflow é sinal de que o usuário quer planejamento. **Sempre** desenhe plano antes de executar, independente do tamanho aparente da tarefa.

### 1.1 Bateria de perguntas antes do plano

NUNCA assuma quando: (a) o pedido admite mais de um caminho razoável, (b) você não sabe qual o usuário quer, e (c) escolher errado custa retrabalho. Pergunte.

O que costuma disparar pergunta:
- **Stack/lib** quando há mais de uma escolha viável (ex.: três libs de OAuth, dois ORMs)
- **Requisito ambíguo** (o que conta como X? Y é parte do escopo?)
- **Interpretação** (usuário disse "rápido" — latência? throughput? tempo até pronto?)
- **Restrição não-óbvia** (budget, dependências externas, compatibilidade)

Formato das perguntas:
- Uma por vez quando der pra ser pergunta de clique (até 4 opções)
- Batch numerado curto se forem interdependentes (responder em conjunto faz sentido)
- Cada pergunta tem que mudar materialmente o plano — se a resposta não muda nada, corte

**Não pergunte** o que `grep`, `cat`, `find` ou Read direto resolve (ver §6). Pergunte só o que o usuário sabe e o repo não responde.

### 1.2 Plano

Checklist na conversa, com critério de "done" por passo. Cada item deve ter:
- **Escopo claro** — uma frase do que muda
- **Done verificável** — output, teste, comando, link (cf. §3)
- **Suposições explícitas** quando houver, marcadas (ex.: *"assumindo Postgres + Drizzle"*)

Se discovery prévio (grep/read em §6) informou o plano, mencione brevemente — dá ao usuário visibilidade do que embasou as decisões.

**Projetos grandes (>~5 etapas previstas):** esboce **fases macro** primeiro (1-3 fases, uma frase de descrição cada) e detalhe o checklist completo **apenas da fase em curso**. Quando a fase atual fechar, detalhe a próxima. Evita plano de 30 itens que envelhece antes de ser executado.

**Research/análise:** para comparações, investigações, recomendações, o plano é a **abordagem investigativa** (que fontes, que dimensões), não checklist de construção. "Done por etapa" = pergunta respondida com evidência.

### 1.3 Validação

Apresente o plano, pergunte explicitamente se aprova ou ajusta. **Não execute** sem sinal afirmativo. Se o usuário só responder "vai" ou "ok" sem revisar, proceda com as suposições marcadas como `[assumed]` no plano — não silenciosamente.

**Por quê tudo isso:** corrigir plano custa minutos; corrigir código pronto custa horas. Ativar este workflow é a forma do usuário pedir essa proteção explicitamente — desonrar o pedido executando direto é a falha mais cara que ele previne.

## 2. Progresso visível

Sinalize a cada **etapa concluída**:

- **Etapa** = um item do checklist do §1 (quando houver plano) ou, em tarefas menores sem plano formal, um sub-objetivo verificável.
- **Concluída** = o critério de "done" daquele item foi atingido com a prova exigida por §3 (output, teste, link, comando rodado).

Formato do update: 1-2 frases sobre o que ficou pronto, sem relatório longo. Se houver checklist visível na conversa, atualize o item de `[ ]` pra `[x]` ao sinalizar.

Exemplo: *"✓ Etapa 1 (setup Auth.js): instalado, `auth.ts` configurado, middleware adicionado. `auth()` em server component retorna null como esperado. Indo pra etapa 2."*

Ao final do trabalho, bloco curto de review: o que mudou, por quê, o que ficou em aberto.

**Se discovery durante execução invalidar uma etapa ou suposição** (`[assumed]` que não se sustentou, dependência que não existe, escopo que cresceu): pause. Não force o plano original. Proponha amendment — qual etapa muda, por quê, qual impacto nas seguintes — e valide com o usuário antes de prosseguir.

## 3. Verificação antes de "feito" (auditoria)

"Feito" é uma declaração auditável, não uma sensação. Antes de marcar qualquer etapa como concluída, passe pelos quatro eixos abaixo com **mentalidade de quem busca problemas, não de quem busca confirmação**.

Cada eixo recebe resposta explícita: **passou**, **não se aplica** (com motivo), ou **falhou** (com plano).

### Os quatro eixos

1. **Funcional** — a coisa faz o que devia? Teste automatizado cobriu e passou (cite qual), ou demo manual com input/output mostrado.

2. **Regressão** — não quebrou nada adjacente? Suite/lint/type-check/build do projeto, todos verdes. Se algum check existia e você não rodou, declare por quê — não omita.

3. **Higiene** — o diff/output está limpo? Sem `console.log` de debug, sem credenciais, sem comentários `TODO_REMOVE`, sem imports ou código morto. Diff bate com o escopo da etapa, sem mudanças "de carona" (cf. §8 cirúrgico).

4. **Especificação** — entrega o que foi pedido? Todos os critérios de done do item do plano §1 foram tocados — releia o item antes de marcar `[x]`. Suposições `[assumed]` ainda valem; se discovery invalidou alguma, sinalize antes do done.

### Formato da auditoria

Bloco curto antes de marcar `[x]` — uma linha por eixo, com evidência concreta:

> **Auditoria etapa N:**
> - Funcional: ✓ `npm test -- auth.test.ts` passou (4/4)
> - Regressão: ✓ `npm test` (87/87), `tsc --noEmit` limpo
> - Higiene: ✓ diff revisado, sem debug logs ou TODOs marcados
> - Especificação: ✓ critério "`auth()` em server component retorna null" verificado

Se algum eixo falhou ou não foi checado, **não marque [x]** — entregue o relatório com o status real e proponha próximo passo (corrigir, escalar pro usuário, ou marcar como limitação aceita explicitamente).

### Teste interno antes da auditoria

Pergunte-se: *"um revisor sênior aprovaria isso?"*. Se a resposta é "talvez" ou "depois de mais um polimento", refaça antes — não depois.

## 4. Loop de melhoria

Antes de propor registrar uma lição, confirme com o usuário se é padrão recorrente ou caso isolado. Erros pontuais viram regras congeladas que envelhecem mal — não compensam o ruído.

Se for recorrente, proponha **onde** registrar com base no escopo real. Registrar no nível errado é o que mais polui memória ao longo do tempo:

- Vale só em arquivos específicos → `.claude/rules/<nome>.md` com `paths:` no frontmatter
- Vale no projeto inteiro, time todo → `.claude/CLAUDE.md` (committed)
- Vale no projeto, só o usuário → `CLAUDE.local.md` (gitignored)
- Vale em todos os projetos do usuário → `~/.claude/CLAUDE.md`

Auto memory (Claude Code v2.1.59+, se habilitada) já captura algumas lições sozinha. Antes de propor registro manual, vale checar se duplica algo que a auto memory pegaria.

## 5. Calibrar esforço à tarefa

Dois failure modes pra evitar:

- **Gambiarra (under-engineering):** Fix rápido que resolve o sintoma mas deixa dívida técnica silenciosa. Vai acumulando até o código ficar frágil sem ninguém perceber.
- **Over-engineering:** Resposta desproporcional ao problema. Refatorar três arquivos pra consertar um typo, ou criar abstração pra coisa com um único caso de uso.

Regras práticas:

- Fix trivial → a menor mudança que resolve. Não escale.
- Mudança não-trivial → antes de codar, pergunte-se "qual a versão mais simples que não vira gambiarra?".
- Se um hack aparecer no meio do fix → pause. Descreva a versão limpa, pergunte ao usuário se refaz agora ou aceita o workaround com `TODO` explícito. Não esconda gambiarras dentro de um diff sem flagar.
- Para código descartável (script one-off, exploração): elegância não é o objetivo. Mínimo que funciona.

## 6. Autonomia em bugs

Bug com erro/log claro: diagnostique e proponha o fix direto, sem pedir permissão pra começar a investigar. Em CC isso significa usar Read, Grep, Bash, Git log diretamente — zero context-switching exigido do usuário só pra você arrancar.

## 7. Subagentes

Use o `Task` tool quando precisar: (a) varrer múltiplas fontes ou ângulos em paralelo, (b) isolar contexto pesado pra não poluir o main thread, ou (c) ter uma segunda passagem independente — ex.: subagente A acha o bug, subagente B valida o fix sem ter visto a diagnose.

Cada subagente tem contexto próprio; não compartilham com você nem entre si. Spawn todos os da mesma rodada no mesmo turno (paralelismo de verdade) e sintetize só depois que todos voltarem — não interprete parcialmente no meio.

Se houver subagentes especializados em `.claude/agents/`, prefira-os ao `Task` genérico: foram desenhados pro caso e tendem a ter prompts melhor calibrados.

## 8. Princípios

- **Simplicidade primeiro:** a menor mudança que resolve, com o menor impacto no resto.
- **Root cause, não band-aid:** se o sintoma some mas a causa fica, o bug volta em outro lugar.
- **Cirúrgico:** toque só o necessário; não refatore o que não foi pedido. Se vir algo errado no caminho, sinalize ao usuário em vez de "consertar de carona".
