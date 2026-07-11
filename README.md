# wizard-claude

**The power of [Wizard](https://github.com/teddytennant/wizard), on your Claude subscription.**

Wizard is a sovereign, self-extending agent — one Rust binary, any model. This
repo is its loadout ported to a [Claude Code plugin](https://docs.anthropic.com/en/docs/claude-code/plugins):
the same evolved skills, the same four specialist subagents, and Claude Code
re-implementations of Wizard's signature `/fusion` and `/evolve`, running on
the Claude Code harness you already pay for. No API key, no second agent to
babysit.

## Install

Inside Claude Code:

```
/plugin marketplace add teddytennant/wizard-claude
/plugin install wizard@wizard-claude
```

## What you get

### Skills (auto-loaded when relevant)

| Skill | What it does |
|-------|--------------|
| `wizard-coding` | Wizard's bundled coding workflow — the AHE-evolved bundle that hit 100% pass@1 on the Terminal-Bench sample. Explore before editing, minimal diffs, verify with the project's own tests, clean deliverable trees, plus battle-tested recipes for spec/report contracts, long-lived services, native-extension installs, and puzzle analysis. |
| `wizard-evolve` | Wizard's prime directive — *build the capability, don't complain about lacking it* — mapped to Claude Code's channels: skills, MCP servers, scripted tools, slash commands, subagents, hooks. |

### Subagents (delegate with the Task tool, or via `/agents`)

| Agent | Role |
|-------|------|
| `reviewer` | Read-only code review: correctness, security, contract breaks. Ends with APPROVE / APPROVE-WITH-NITS / REQUEST-CHANGES. |
| `tester` | The run-diagnose-fix test loop, until green or precisely explained. |
| `documenter` | Docs that match the code — reads source first, never touches logic. |
| `researcher` | Sourced web research; primary sources over blog recaps. |

### Commands

| Command | What it does |
|---------|--------------|
| `/wizard:fusion <task>` | Wizard's model-fusion debate, re-hosted: a panel of independent advisor subagents drafts and critiques answers, then the main agent synthesizes and acts as the sole tool-caller. In Wizard, fused panels tend to beat their best single member; here the panel is independent contexts with adversarial lenses instead of independent providers. |
| `/wizard:evolve <capability>` | Self-extension: add a skill, MCP server, scripted tool, slash command, subagent, or hook through the cheapest channel that works, verify it's live, and log it to `~/.claude/evolution.jsonl`. |

### Not bundled (on purpose)

Wizard's default loadout ships a Playwright MCP browser. This plugin doesn't
auto-register it — a browser server spawning in every session is exactly the
kind of standing bloat Wizard's ladder says to avoid. When a task needs a
browser, `/wizard:evolve browser use` adds it, or do it yourself:

```bash
claude mcp add playwright -- npx -y @playwright/mcp@latest
```

## Fidelity

Ported from [`teddytennant/wizard`](https://github.com/teddytennant/wizard)
at v1.0.0 (`8d08845`). Skills and subagent prompts are verbatim where
possible; only harness bindings changed (`read_file`→Read, `execute`→Bash,
`spawn_subagent`→Task, `~/.wizard/`→`~/.claude/`). Features that only make
sense inside Wizard's own binary — live provider switching, deep evolve's
self-recompile, `/publish` fork distribution, `wizard bench` — stay in
Wizard; if you want those, [install the real thing](https://github.com/teddytennant/wizard#readme).

## License

MIT — same as Wizard.
