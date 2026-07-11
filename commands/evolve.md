---
description: Give Claude Code a new capability Wizard-style - skill, MCP server, scripted tool, subagent, or hook - via the cheapest channel that works
argument-hint: <capability to add>
---

Give this Claude Code install a new capability, following Wizard's evolve
ladder (see the wizard-evolve skill for the full channel reference). The
capability to add:

$ARGUMENTS

If no capability is named above, look at what the current conversation was
blocked on and propose the evolution that would unblock it.

Procedure:

1. **Pick the cheapest channel that works**, in this order:
   skill (knowledge/workflow) → MCP server (external capability: browser,
   database, search, computer use) → scripted tool (small glue) → slash
   command (repeatable prompt) → subagent (specialist worker) → hook
   (guaranteed automation on harness events). State your pick and why in
   one sentence before building.
2. **Decide scope.** Project-level (`.claude/`, shared with the repo) when
   the capability is about this project; user-level (`~/.claude/`) when it
   should follow the user everywhere. Prefer project scope when unsure.
3. **Build it.** One file (or one config block) per capability, so it stays
   independently reversible. For MCP servers, verify the command or URL
   exists on this machine before registering (`which npx`, `curl -sI`).
4. **Verify it is actually live** before reporting success: `claude mcp list`
   for servers, run the script once for scripted tools, read the file back
   for skills/agents/commands. If it only takes effect on the next session
   start, say so explicitly.
5. **Log it.** Append one JSON line to `~/.claude/evolution.jsonl`:
   `{"ts": "<iso8601>", "channel": "skill|mcp|tool|command|subagent|hook",
   "name": "...", "path": "...", "why": "..."}`.
6. **Report** what was added, where the file lives, and how to remove it —
   every evolution must be one deletion away from undone.

Never add a capability the user did not ask for, and never fabricate
success: if the server won't start or the dependency is missing, report the
real error and what you tried.
