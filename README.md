# dev-loop

A planning-first coding workflow for [Claude Code](https://claude.com/claude-code), as a plugin. Every code change goes through the same loop: ground in the code, design with you, prove the plan against an adversary, build test-first, verify with evidence. Out-of-scope finds become plans instead of scope creep, and merge conflicts are resolved by intent instead of by "take theirs".

Works in plain Claude Code. No other harness needed.

## Install

```
/plugin marketplace add bakonblitz-debug/dev-loop
/plugin install dev-loop@dev-loop
```

Or from a local clone (edits to the skills are live in the next session):

```
git clone https://github.com/bakonblitz-debug/dev-loop
/plugin marketplace add /path/to/dev-loop
/plugin install dev-loop@dev-loop
```

The plugin is meant to be enabled per project (it changes how Claude approaches code): enable it in the project's `.claude/settings.json` under `enabledPlugins` rather than globally.

`architect` conducts skills from the [superpowers](https://github.com/obra/superpowers) plugin (brainstorming, writing-plans, subagent-driven-development, test-driven-development, verification-before-completion, finishing-a-development-branch). Install it for the full pipeline; `ouroboros`, `handoff` and `git-conflict` work without it.

## Commands

| Command | What it does |
|---|---|
| `/dev-loop:architect` | Conductor for any code change, small ones included: runs brainstorming → ouroboros → writing-plans → subagent build → verification. Skips only pure reads, one-line typo edits, or "skip planning". |
| `/dev-loop:ouroboros` | Adversarial planner/attacker loop, then an independent verifier; ingests a brainstorming design instead of re-asking. Use before risky or costly changes. |
| `/dev-loop:handoff` | An out-of-scope bug, missing test, refactor or TODO found mid-task becomes a focused plan in the plans folder, delegated instead of fixed inline. |
| `/dev-loop:git-conflict` | Resolves merge and rebase conflicts, in code and in notes or memory repos. |
| `/dev-loop:list` | This table, generated from the skill files. |

Claude also picks the skills up from what you say ("add a feature", "stress-test this plan", "unrelated but…", "merge conflict").

## Settings

All optional; each has a default.

| What | Where | Default |
|---|---|---|
| Plans folder (designs, plans, handoffs) | name it in your CLAUDE.md | `.plans/` at the repo root |
| A command that lists open handoffs, for `handoff` to check against | name it in your CLAUDE.md | re-read the file |
| Cross-model verifier for `ouroboros` | `VERIFIER_CMD` in your CLAUDE.md, e.g. a CLI call to another model family | a fresh Claude subagent, marked provisional |
| Coding rules printed at session start (the `coding-rules` hook) | `CODING_RULES_FILE` in the `env` block of `~/.claude/settings.json` | `<project>/.claude/coding-rules.md`, else nothing |

Handoff files carry their state under a `## Status` heading (`open — <short text>` or `done`), so any tool can list the open ones.

## Develop

```
git config core.hooksPath .githooks
```

The pre-commit hook runs `scripts/skill.py lint` (skill rules, plus the public-plugin check: no machine-specific paths, local tool names or owner voice) and `.githooks/commit-guard.py` (lost exec bits, CRLF). `bash scripts/py.sh scripts/skill.py --selftest` and `bash hooks/coding-rules.sh --selftest` test the scripts.

## License

MIT, see [LICENSE](LICENSE).
