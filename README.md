# claude-skills

A shared [Claude Code](https://code.claude.com) plugin marketplace. Install the plugins here to get their
skills in every Claude Code session.

## Plugins

### `project-bootstrap`

Starts and maintains a lightweight project-discipline system so software work is documented, reversible,
and resumable across sessions — instead of re-explaining your conventions every time. It provides:

- A **`CLAUDE.md` anchor** that makes every session auto-load the project's state and rules.
- Living docs: **PROJECT_STATE**, **ROADMAP**, **conventions**, **ADRs**, a dated **research log**, a
  **glossary**, and an **assumptions & risks** register — each with a template.
- Three routines: **KICKOFF** (new project), **ADOPT** (retrofit an existing undocumented repo from its
  code + git history), and **RESUME/DONE** (read state on start, close stages cleanly).
- A **plan-before-execute** habit and clear triggers for when a decision earns an ADR.

## Install

In an interactive Claude Code session:

```
/plugin marketplace add MikhailBukach/claude-skills
/plugin install project-bootstrap@claude-skills
```

Or point at the full git URL:

```
/plugin marketplace add https://github.com/MikhailBukach/claude-skills.git
```

After installing, restart Claude Code. The skill triggers on its own when you start, adopt, or resume a
project — or ask for it explicitly.

## Update

```
/plugin marketplace update claude-skills
/plugin update project-bootstrap@claude-skills
```

## Layout

```
claude-skills/
├── .claude-plugin/
│   └── marketplace.json          # marketplace catalog
└── plugins/
    └── project-bootstrap/
        ├── .claude-plugin/
        │   └── plugin.json       # plugin manifest
        └── skills/
            └── project-bootstrap/
                ├── SKILL.md
                ├── templates/    # CLAUDE.md, PROJECT_STATE, ADR, glossary, risks, …
                └── reference/     # kickoff-interview, decision-triggers, definition-of-done, …
```

## License

MIT — see [LICENSE](LICENSE).
