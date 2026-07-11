---
description: Run this turn as a Wizard-style fusion panel - independent advisors debate, then you synthesize the final tool-capable answer
argument-hint: <question or task>
---

Run this request through Wizard's fusion architecture, adapted to Claude
Code. In Wizard, `/fusion` runs a panel of independent models as a debate
and a synthesizer merges their answers; fused answers tend to beat the best
single panelist because the advisors catch each other's mistakes. Here the
panel is independent subagent contexts instead of independent providers —
same structure: advisors advise, one actor acts.

The request:

$ARGUMENTS

If the request above is empty, fuse the user's most recent substantive
request in this conversation instead.

## 1. Panel (advisors)

Spawn 3 general-purpose subagents **in parallel** (a single message with
multiple Task calls). Each gets the same self-contained task statement —
the request plus any file paths and constraints from the conversation it
needs, since it cannot see the chat — but a different assigned lens, for
example:

- **Advisor A — correctness-first:** the most rigorous, defensible answer;
  flag every assumption and edge case.
- **Advisor B — pragmatic:** the simplest thing that fully works; minimal
  moving parts, least code, least risk.
- **Advisor C — adversarial:** attack the obvious approach; surface failure
  modes, hidden constraints, and a contrarian alternative if one is viable.

Panel rules, stated in each advisor's task: advisors are **advisory only** —
they may read, search, and investigate, but must not edit files, run
state-changing commands, or produce side effects. Each returns a complete
draft answer or plan, with its reasoning and its confidence.

## 2. Critique round

After all drafts return, run one critique round (Wizard's default
`rounds = 1`): spawn the same 3 lenses again in parallel, each given all
three anonymized drafts, asked to critique the others' mistakes and
omissions and submit a revised final draft. Skip this round only if the
drafts already agree substantively — say so if you skip it.

## 3. Synthesis (the actor)

You are the synthesizer, and the **sole tool-caller**. Merge the panel's
final drafts into one answer: adopt the strongest reasoning, resolve
disagreements explicitly (say which advisor was right and why), and discard
what didn't survive critique. Then **act on it** — if the request needs
edits, commands, or files, you make them yourself, informed by the fused
plan. Never let advisors' conflicting suggestions turn into conflicting
actions; all action flows through you.

Finish with a short note of where the panel disagreed and which line you
took. Fusion is expensive — several times the tokens of a single turn — so
it's for turns where being right matters more than being fast.
