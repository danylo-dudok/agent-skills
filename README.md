# loopsmith — Feature Implementation Orchestrator

A Claude Code skill that turns a one-line feature request into an autonomous, verified implementation run. It gathers context, asks targeted questions, assembles a structured loop prompt, writes a failing test harness, then launches `/goal` to drive the work to green.

## Install

Copy the skill into your Claude Code skills directory:

    cp -r skills/loopsmith ~/.claude/skills/

Then invoke it in Claude Code:

    /loopsmith add retry logic to the ingestion pipeline

## How it works

1. **Reads context** — your KB index (optional), available skills, and the working directory's stack and conventions.
2. **Asks** — a single batch of targeted questions: what to build, done criteria, scope, forbidden operations.
3. **Assembles** a loop prompt with hard safety limits — it never commits, pushes, publishes, or applies infrastructure.
4. **Creates a failing test harness** and confirms it starts red.
5. **Launches `/goal`** to iterate until the harness goes green, then writes a per-file change summary. All edits are left uncommitted for you to review.

## Requirements

- **`/goal`** (Claude Code ≥ 2.1.139) — required to launch the autonomous loop. Without it, the skill prints the assembled prompt for you to paste manually.
- **`/senior-architect`** (optional) — invoked for architectural review when a feature adds dependencies, storage, or modules. Safely skipped if not installed.
- **KB index** (optional) — set your knowledge-base index path in the Configuration block at the top of `skills/loopsmith/SKILL.md`. Skipped if unset or not found.

## License

No license yet — add one if you want others to reuse it.
