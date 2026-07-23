# Scaffolding IA

Guia para iniciar projetos com agentes de IA, usando workflows estruturados e skills reutilizáveis.

## Instalando RTK (economia de tokens)

O [RTK](https://github.com/rtk-ai/rtk) é um CLI proxy que comprime a saída de comandos comuns (`git`, `ls`, testes, linters, etc.) antes de chegar ao contexto do LLM — tipicamente **60–90% menos tokens** em chamadas de shell.

### 1. Instalar o binário

```bash
curl -fsSL https://raw.githubusercontent.com/rtk-ai/rtk/refs/heads/master/install.sh | sh
```

O instalador coloca o binário em `~/.local/bin/rtk`. Confirme:

```bash
rtk --version
```

Se o comando não for encontrado, adicione `~/.local/bin` ao `PATH` (ex.: no `~/.zshrc`):

```bash
export PATH="$HOME/.local/bin:$PATH"
```

Depois rode `source ~/.zshrc` (ou abra um novo terminal).

### 2. Vincular ao Cursor

```bash
rtk init -g --agent cursor
```

Isso registra o hook em `~/.cursor/hooks.json` (`preToolUse` → `rtk hook cursor`), que reescreve comandos de shell automaticamente (ex.: `git status` → `rtk git status`).

### 3. Reiniciar o Cursor

Feche e abra o Cursor (ou recarregue a janela) para o hook passar a valer nas sessões do Agent.

### 4. Verificar a instalação

```bash
rtk init --show
```

Procure a linha:

```text
[ok] Cursor hook: registered in hooks.json
```

O arquivo `~/.cursor/hooks.json` deve conter algo como:

```json
{
  "version": 1,
  "hooks": {
    "preToolUse": [
      {
        "command": "rtk hook cursor",
        "matcher": "Shell"
      }
    ]
  }
}
```

### 5. Conferir economia de tokens

Depois de usar o Agent com comandos de shell:

```bash
rtk gain
```

Teste manual de compressão:

```bash
# Comparar saída crua vs filtrada
git status
rtk git status

rtk hook check "git status"   # deve sugerir: rtk git status
```

### Observações

- O hook só intercepta a ferramenta **Shell**. `Read`, `Grep` e `Glob` nativos do Cursor não passam pelo RTK.
- Para desvincular: `rtk init -g --agent cursor --uninstall`
- Docs: [rtk-ai/rtk](https://github.com/rtk-ai/rtk) · [agentes suportados](https://www.rtk-ai.app/docs/getting-started/supported-agents/)

---

## Instalando skill tlc-spec-driven

A skill **tlc-spec-driven** (Tech Lead's Club — Spec-Driven Development) guia o agente em 4 fases adaptativas:

```
Specify → Design → Tasks → Execute
```

Ela planeja features com requisitos rastreáveis, tasks atômicas, verificação independente e memória persistente em `.specs/`.

### Pré-requisito

- Node.js instalado (para usar `npx`)

### Instalação rápida

Na raiz do projeto:

```bash
npx @tech-leads-club/agent-skills install -s tlc-spec-driven
```

### Instalação interativa

```bash
npx @tech-leads-club/agent-skills
```

O wizard pede:

1. **Ação** — instalar ou atualizar skills  
2. **Skill** — selecionar `tlc-spec-driven`  
3. **Agentes** — Cursor, Claude Code, Windsurf, etc.  
4. **Método** — Copy (recomendado) ou Symlink  
5. **Escopo** — Local (só este projeto) ou Global (home do usuário)

### Opções úteis

```bash
# Instalar só no Cursor e Claude Code
npx @tech-leads-club/agent-skills install -s tlc-spec-driven -a cursor claude-code

# Instalação global (disponível em todos os projetos)
npx @tech-leads-club/agent-skills install -s tlc-spec-driven -g

# Atualizar a skill
npx @tech-leads-club/agent-skills update -s tlc-spec-driven

# Forçar re-download
npx @tech-leads-club/agent-skills install -s tlc-spec-driven --force
```

### Onde a skill fica instalada

| Agente       | Caminho local do projeto              |
| ------------ | ------------------------------------- |
| Cursor       | `.cursor/skills/tlc-spec-driven/`     |
| Claude Code  | `.claude/skills/tlc-spec-driven/`     |
| Windsurf     | `.windsurf/skills/tlc-spec-driven/`   |

### Como usar depois de instalar

Fale naturalmente com o agente. Exemplos de gatilhos:

| Objetivo              | Exemplo de prompt                          |
| --------------------- | ------------------------------------------ |
| Planejar uma feature  | `Specify feature [nome]`                   |
| Discutir ambiguidades | `Discuss feature [nome]`                   |
| Desenhar arquitetura  | `Design feature [nome]`                    |
| Quebrar em tasks      | `Break into tasks` / `Create tasks`        |
| Implementar           | `Implement task` / `Execute`               |
| Validar               | `Validate` / `Verify work` / `UAT`         |
| Pausar / retomar      | `Pause work` / `Resume work`               |

Documentação oficial: [tech-leads-club/agent-skills](https://github.com/tech-leads-club/agent-skills) · skill por [Felipe Rodrigues](https://github.com/felipfr)
