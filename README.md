# Formgong agent skills

Agent skills (`SKILL.md`) that teach coding agents — Claude Code, Cursor, Codex, Copilot and other tools that read skills — to add a contact form that actually delivers, using [Formgong](https://formgong.com) as the form backend.

| Skill | What it does |
| --- | --- |
| [`formgong-contact-form`](skills/formgong-contact-form/SKILL.md) | Adds a working contact, quote or lead form to a static or AI-built site (Lovable, Bolt, v0, Cursor, plain HTML) with no backend, and fixes forms that show "Message sent" but send nothing. |

## Install

```bash
npx skills add formgong/skills
```

Or copy `skills/formgong-contact-form/` into your agent's skills folder (for Claude Code: `~/.claude/skills/` or `.claude/skills/` in the project).

## Related

- [agents.md](https://formgong.com/agents.md) — the same rules as a single file for agents
- [Remote MCP server](https://github.com/formgong/mcp) — lets an agent create the form and fetch the snippet itself
- [Starters](https://github.com/formgong/html-starter) for HTML, [Next.js](https://github.com/formgong/nextjs-starter), [Astro](https://github.com/formgong/astro-starter) and [React](https://github.com/formgong/react-contact-form)

MIT licensed.
