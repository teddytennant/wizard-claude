# wizard-claude

A Claude Code plugin with the skills, subagents and two slash commands from
[Wizard](https://github.com/teddytennant/wizard), my Rust terminal coding
agent, rewritten to use Claude Code's tools. It's ported from Wizard v1.0.0
(`8d08845`).

## Install

Inside Claude Code:

```
/plugin marketplace add teddytennant/wizard-claude
/plugin install wizard@wizard-claude
```

## What's in it

Skills:

- `wizard-coding`: Wizard's coding workflow (read before editing, small diffs, run the project's tests, review the diff) plus notes on spec-driven tasks, long-running services and native package installs.
- `wizard-evolve`: how to add a capability to Claude Code (skill, MCP server, script, command, subagent or hook), cheapest option first.

Subagents:

- `reviewer`: read-only review that ends in APPROVE, APPROVE-WITH-NITS or REQUEST-CHANGES.
- `tester`: runs the test suite and fixes failures until it passes or the failure is explained.
- `documenter`: edits docs and comments to match the code, never the logic.
- `researcher`: web research that reports back with sources.

Commands:

- `/wizard:fusion <task>`: three advisor subagents (correctness, pragmatic, adversarial) draft and critique answers, then the main agent merges them and is the only one that edits or runs anything.
- `/wizard:evolve <capability>`: adds a capability using the `wizard-evolve` ladder, checks it works, and logs it to `~/.claude/evolution.jsonl`.

The Playwright browser MCP that Wizard ships with isn't registered. Add it if you want it:

```
claude mcp add playwright -- npx -y @playwright/mcp@latest
```

## License

MIT
