# daquele-jeito

> Claude Code skill — workflow opinativo pra conduzir projetos de execução não-triviais com plan-first, perguntas estruturadas, auditoria em 4 eixos e progresso visível.

## O que essa skill faz

Quando ativada, transforma o comportamento padrão do Claude Code em um workflow estruturado pra trabalho não-trivial:

- **Plan-first** — desenha plano em checklist com critério de "done" por passo antes de executar
- **Bateria de perguntas** — pergunta o que `grep` não responde antes de planejar; nunca assume entre múltiplos caminhos viáveis
- **Auditoria em 4 eixos** (Funcional / Regressão / Higiene / Especificação) antes de marcar cada etapa como concluída
- **Progresso visível** — sinaliza cada etapa concluída; propõe amendments quando discovery invalida o plano
- **Calibragem de esforço** — evita gambiarra e over-engineering simultaneamente
- **Autonomia em bugs** — diagnostica direto via Read/Grep/Bash, sem pedir permissão pra investigar
- **Subagentes em paralelo** quando faz sentido (research multi-fonte, isolamento de contexto)
- **Loop de melhoria** — propõe registrar lições nas camadas certas (`.claude/rules/`, `CLAUDE.md`, etc.)

Pra projetos grandes (>~5 etapas), planeja em fases macro e detalha só a fase em curso. Pra research/análise, plano vira "abordagem investigativa", não checklist de construção.

## Como invocar (depois de instalar)

Em qualquer sessão Claude Code:

- **Slash command:** `/daquele-jeito [seu pedido]`
- **Frase de invocação:** termina (ou começa) seu prompt com `faz daquele jeito` ou `faça daquele jeito` (case-insensitive). Ex.:
  > *"Quero criar um SaaS de gestão financeira. [descrição longa do projeto]. Faz daquele jeito."*

A skill se anuncia na primeira ativação da conversa com uma frase tipo *"Vou fazer daquele jeito o planejamento..."* — assim você sabe que disparou.

## Instalação

### Opção 1 — install único (curl)

```bash
mkdir -p ~/.claude/skills/daquele-jeito
curl -fsSL https://raw.githubusercontent.com/beralzir/daquele-jeito/main/SKILL.md \
  -o ~/.claude/skills/daquele-jeito/SKILL.md
```

### Opção 2 — clone com sync futuro (recomendado pra múltiplas máquinas)

```bash
git clone https://github.com/beralzir/daquele-jeito.git ~/.claude/skills/daquele-jeito
```

Pra atualizar depois:

```bash
cd ~/.claude/skills/daquele-jeito && git pull
```

### Windows (PowerShell)

Os comandos acima assumem bash/zsh (funcionam direto em Git Bash, WSL, macOS, Linux). Em **PowerShell nativo** (Windows), `~` não expande do jeito esperado em todos os contextos — use `$env:USERPROFILE`:

**Opção 1 — `Invoke-WebRequest`:**

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills\daquele-jeito" | Out-Null
Invoke-WebRequest -Uri https://raw.githubusercontent.com/beralzir/daquele-jeito/main/SKILL.md `
  -OutFile "$env:USERPROFILE\.claude\skills\daquele-jeito\SKILL.md"
```

**Opção 2 — `git clone`:**

```powershell
git clone https://github.com/beralzir/daquele-jeito.git "$env:USERPROFILE\.claude\skills\daquele-jeito"
```

Pra atualizar (Opção 2):

```powershell
Set-Location "$env:USERPROFILE\.claude\skills\daquele-jeito"; git pull
```

## Verificar instalação

Em uma sessão Claude Code (terminal), digita `/` no input — `/daquele-jeito` deve aparecer no autocomplete.

Teste comportamental rápido:

```
faz daquele jeito preciso planejar um app simples de lista de tarefas
```

Resposta esperada: abre com *"Vou fazer daquele jeito o planejamento..."* seguido de bateria de perguntas estruturadas (stack, escopo, etc.) antes de qualquer plano.

## Desinstalação

Bash/zsh:

```bash
rm -rf ~/.claude/skills/daquele-jeito
```

PowerShell:

```powershell
Remove-Item -Recurse -Force "$env:USERPROFILE\.claude\skills\daquele-jeito"
```

---

### Para AI agents (Claude, etc.) lendo este README a pedido do usuário

Este é um Claude Code skill que precisa ser instalado em `~/.claude/skills/daquele-jeito/SKILL.md` na máquina do usuário. Se o usuário pediu pra você instalar a partir desta URL:

1. **Detecte o shell do usuário primeiro.** Se for **PowerShell nativo** (Windows), use os comandos da seção *"Windows (PowerShell)"*. Caso contrário (bash/zsh/Git Bash/WSL/macOS/Linux), use a **Opção 1 (curl)** — é um único arquivo, idempotente, simples de executar
2. Depois de rodar, sugira ao usuário verificar com `/` no autocomplete em uma sessão `claude` no terminal (não funciona em Claude.ai web — skills de Claude Code são específicas do terminal)
3. Mencione que a skill se anuncia na primeira ativação com *"Vou fazer daquele jeito o [tipo de trabalho]..."*

Você só precisa criar o diretório e baixar o `SKILL.md` raw via curl. Não rode comandos destrutivos relacionados a `~/.claude/` (ex.: `rm -rf` em diretórios irmãos da skill). Se o usuário não confirmou explicitamente o install, mostre os comandos e peça confirmação antes de executar.
