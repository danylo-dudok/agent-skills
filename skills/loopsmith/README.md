# loopsmith

> Describe a feature in one line. loopsmith asks the right questions, writes a failing test, and drives an autonomous agent until it passes — then shows you a diagram of what changed and hands you the diff to review.

A Claude Code skill that turns a one-line feature request into an autonomous, **test-verified** implementation run. It front-loads the requirements, builds a red→green harness, and delegates execution to `/goal` — behind hard safety rails so the loop never commits, pushes, publishes, or touches infrastructure.

## Why loopsmith

- **Test-first, not vibes.** Every run starts by writing a failing check and confirming it's red. The agent works until it's green — so "done" means *verified*, not *claimed*.
- **Safe by construction.** Hard limits the loop cannot override: no commits, pushes, publishes, infra applies, prod migrations, or external write calls. Every change is left **uncommitted** for you to review and commit yourself.
- **Reviewed, not just passing.** Before declaring done, an independent reviewer (a separate skill or a fresh subagent — never the author) checks the diff for scope, correctness, and safety.
- **Visual at both ends.** A Mermaid **plan** diagram before launch and a Mermaid **recap** after — renders in Obsidian, GitHub, and VS Code, no external tool.

## Requirements

- **[Claude Code](https://claude.com/claude-code)** — loopsmith is a Claude Code skill.
- **`/goal`** (Claude Code ≥ 2.1.139) — required to launch the autonomous loop. Without it, loopsmith prints the assembled prompt for you to run manually.
- **`/senior-architect`** *(optional)* — pulled in for an architecture check when a feature adds dependencies, storage, or new modules. Safely skipped if not installed.

## Installation

```bash
git clone https://github.com/danylo-dudok/agent-skills.git
cp -r agent-skills/skills/loopsmith ~/.claude/skills/
```

Confirm it's installed, then open (or restart) Claude Code and type `/loopsmith`:

```bash
ls ~/.claude/skills/loopsmith/SKILL.md
```

> **Update:** `git pull`, then re-run the `cp -r`.
> **Uninstall:** `rm -rf ~/.claude/skills/loopsmith`.

## Usage

Invoke with a short description of what to build:

```bash
/loopsmith add retry logic to the ingestion pipeline
/loopsmith add a dim_customers model joining orders and customers
/loopsmith Terraform module for a Databricks job cluster
```

loopsmith asks a focused batch of questions (what to build, done criteria, scope, forbidden operations), shows you a **plan diagram** plus the failing test, and launches once you reply **go**.

## How it works

1. **Reads context** — your KB index (optional), available skills, and the working directory's stack and conventions.
2. **Asks** — one batch of targeted questions: what to build, done criteria, scope, forbidden operations.
3. **Plans** — assembles a loop prompt with hard safety limits and shows it next to a Mermaid **plan diagram** for your approval.
4. **Writes a failing test harness** and confirms it starts red.
5. **Launches `/goal`** to iterate until the harness goes green, gets an independent diff review, then writes a Mermaid **recap** plus a per-file change summary. All edits stay uncommitted for your review.

## Configuration

loopsmith can optionally pull context from a personal knowledge base. Edit the **Configuration** block at the top of `SKILL.md`:

```
> KB index path: `~/notes/wiki/index.md` — replace with your own knowledge-base index path.
```

If the path is unset or not found, the KB step is skipped — loopsmith works fine without it.

## License

No license yet. Add one (e.g. [MIT](https://choosealicense.com/licenses/mit/)) if you want others to reuse and contribute.
