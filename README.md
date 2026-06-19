# agent-skills

A collection of [Claude Code](https://claude.com/claude-code) skills by [@danylo-dudok](https://github.com/danylo-dudok).

## Skills

| Skill | What it does |
|-------|--------------|
| **[loopsmith](skills/loopsmith)** | Turns a one-line feature request into an autonomous, test-verified implementation run — asks the right questions, writes a failing test, drives `/goal` to green, reviews its own diff, and hands you a visual recap. |

## Install a skill

Each skill lives in `skills/<name>/`. Install one by copying its folder into your Claude Code skills directory:

```bash
git clone https://github.com/danylo-dudok/agent-skills.git
cp -r agent-skills/skills/loopsmith ~/.claude/skills/
```

Then invoke it in Claude Code (e.g. `/loopsmith ...`). See each skill's own README for requirements and details.

## Layout

```
skills/
└── <name>/
    ├── SKILL.md     # the skill itself
    └── README.md    # what it does, requirements, install
```

## Adding a skill

Add `skills/<name>/SKILL.md` (plus a `README.md`), then add a row to the table above.

## License

No license yet — add one if you want others to reuse these skills.
