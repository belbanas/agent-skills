# agent-skills

Portable [Agent Skills](https://agentskills.io) that help developers keep ownership of their code while working with AI — optimizing for understanding, not just for finishing the task.

The skills follow the open Agent Skills standard (`SKILL.md` with `name` + `description`), so they work in Claude, Claude Code, OpenAI Codex, GitHub Copilot, Cursor, Gemini CLI and other compatible agents.

| Skill | What it does |
| --- | --- |
| [`socratic-coding-coach`](plugins/socratic-coding-coach) | Socratic mentor that guides you to the solution with questions and gradual hints instead of giving the answer. |
| [`code-teach-back`](plugins/code-teach-back) | 30-second check that you can explain AI-written code (why it works, why this approach, what could break) before committing it. |

Both skills work in English and Hungarian.

## Installation

The skill folders live at `plugins/<name>/skills/<name>/` — each one is a self-contained directory with a `SKILL.md`.

### Any agent — Skills CLI

The community [`skills`](https://github.com/vercel-labs/skills) CLI finds the skills in this repo and installs them for the agent(s) you choose:

```bash
npx skills add belbanas/agent-skills
```

### Claude Code — plugin marketplace

```
/plugin marketplace add belbanas/agent-skills
/plugin install socratic-coding-coach@belbanas-skills
/plugin install code-teach-back@belbanas-skills
```

### Manual install

Clone the repo and copy (or symlink) a skill folder into your agent's skills directory:

```bash
git clone https://github.com/belbanas/agent-skills.git
cp -r agent-skills/plugins/socratic-coding-coach/skills/socratic-coding-coach <skills-dir>/
```

Common skills directories:

| Agent | Project | Personal (all projects) |
| --- | --- | --- |
| Shared convention — Codex, Gemini CLI, Cursor, VS Code + Copilot, Windsurf, … | `.agents/skills/` | `~/.agents/skills/` |
| Claude Code | `.claude/skills/` | `~/.claude/skills/` |
| GitHub Copilot (coding agent / CLI) | `.github/skills/` | `~/.copilot/skills/` |

Skill discovery locations are still evolving — if a skill isn't picked up, check your agent's documentation for the current path.

### Claude.ai

Zip a skill folder (the one containing `SKILL.md`) and upload it under **Settings → Capabilities → Skills**.

## Repository layout

```
.claude-plugin/marketplace.json     # Claude Code marketplace catalogue (ignored by other agents)
plugins/<plugin>/
  .claude-plugin/plugin.json        # Claude Code plugin manifest (ignored by other agents)
  skills/<skill>/SKILL.md           # the portable skill itself
  README.md
```

## Contributing

Issues and pull requests are welcome. Keep `SKILL.md` frontmatter to the standard fields so the skills stay portable. When changing a skill, bump `version` in both its `plugin.json` and `marketplace.json`, and don't rename existing skills — the `name` is the install ID.

## License

[MIT](LICENSE)
