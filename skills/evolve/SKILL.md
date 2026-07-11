---
name: wizard-evolve
description: How to extend Claude Code's own capabilities, Wizard-style. Use when asked to add a new capability, tool, skill, MCP server, or subagent to Claude Code itself — or when a task needs a capability the harness does not yet have (browser, database, computer use, a domain workflow). Pick the cheapest channel that works and verify it is live before reporting success.
---

# Self-extension (Wizard's /evolve, on Claude Code)

Wizard's prime directive: **when a task needs a capability you do not yet
have, acquire it — don't complain about lacking it.** Treat "I can't browse
the web", "I have no database tool", and "I can't see that format" as work
items. Refuse only after trying and hitting a wall you cannot pass (no
network, no toolchain, a missing credential you cannot obtain), and then say
exactly what you tried and what blocked you.

Add every new capability through the cheapest channel that works. All of
these are plain files the user can review, edit, or delete — keep each
evolution independently reversible.

## Picking a channel

| The capability is… | Channel | Where it lives |
|--------------------|---------|----------------|
| Knowledge, a workflow, domain guidelines | Skill | `~/.claude/skills/<name>/SKILL.md` (user) or `.claude/skills/<name>/SKILL.md` (project) |
| External (browser, computer use, database, search, cloud API) | MCP server | `claude mcp add <name> -- <command>` |
| Small glue or project automation | Scripted tool | An executable script plus a skill that says when to run it |
| A repeatable prompt the user fires by hand | Slash command | `~/.claude/commands/<name>.md` or `.claude/commands/<name>.md` |
| A specialized sub-worker with its own prompt/tool scope | Subagent | `~/.claude/agents/<name>.md` or `.claude/agents/<name>.md` |
| Automated behavior on harness events ("whenever X, do Y") | Hook | `settings.json` hooks |

Rule of thumb: if an MCP server or a script can do it, do that. It's
instant, reversible, and works on every install. Wizard's deepest rung —
recompiling its own binary — has no equivalent here (the binary is
Anthropic's); the deepest rung on Claude Code is authoring a plugin that
bundles skills, agents, commands, and MCP servers for others to install.

## Channels

### Skills

Write `<dir>/skills/<name>/SKILL.md`: `---` frontmatter with `name` and
`description`, then a markdown body. The `description` decides when the
skill is surfaced — make it name the triggers ("Use when…"). Keep skills
focused and imperative: instructions, not essays.

### MCP servers

Register with the CLI (project scope by default; `--scope user` to make it
global):

```bash
claude mcp add playwright -- npx -y @playwright/mcp@latest
# or HTTP:  claude mcp add --transport http <name> <url>
```

This is the right channel for browser use — the canonical "build it"
example. When a task needs the web (reading pages, filling forms, clicking,
computer use), do **not** say you can't browse: register the Playwright MCP
server above, then use its navigate/click/type/snapshot tools. If
`npx`/Node is absent, install it, or fall back to a scripted `curl` fetch
for read-only tasks. Try the real thing before declaring it impossible.
The same pattern covers databases (a Postgres/SQLite MCP server), search,
and computer use. Verify the `command` (or `url`) actually exists on this
machine before registering it.

### Scripted tools

Author a small, self-contained script (shell/Python/JS) somewhere stable
(`~/.claude/tools/` is a good convention), make it executable, and pair it
with a skill or slash command that documents when to run it and with what
arguments. Print results to stdout and exit non-zero on failure. Test the
script once with Bash before declaring the evolution done.

### Subagents

Write `<dir>/agents/<name>.md`:

```markdown
---
name: reviewer
description: Audits diffs for security issues. Use after nontrivial changes.
tools: Read, Grep, Glob, Bash
---

You are a security reviewer. Examine the diff for injection, authz, and
secret-handling flaws. Report findings with file:line.
```

Keep `tools` as narrow as the job allows; a subagent runs in an isolated
context and returns one final report.

### Hooks

For "whenever X happens, do Y" automation, add a hook to `settings.json`
(user or project). Hooks are executed by the harness, not by the model, so
they are the only channel that can guarantee an automated behavior.

## Always

- Log every evolution by appending one JSON line to
  `~/.claude/evolution.jsonl` (`{"ts", "channel", "name", "path", "why"}`),
  and tell the user what was added and where the file lives so they can
  review or delete it.
- One file (or one config block) per capability. Keep evolutions
  independently reversible.
- After writing the files, confirm the capability is actually present
  before reporting success: `claude mcp list` for servers, `/agents` for
  subagents, invoke the skill/command once for the rest. Skills, agents,
  and commands are picked up on the next session start — say so if a
  restart is needed.
- Safety: MCP servers and scripted tools run with the user's privileges
  and can make their own network and system calls. Prefer well-known
  servers and small auditable scripts; never add a capability the user did
  not ask for.
