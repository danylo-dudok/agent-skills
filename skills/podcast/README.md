# podcast

> Point it at a file, URL, or topic. `podcast` writes a two-host conversation about it and plays it out loud in two voices — a local, terminal-native take on NotebookLM's Audio Overview.

A Claude Code skill that turns any source into a short **two-host audio podcast** and plays it automatically. Claude writes the dialogue; [Voicebox](https://voicebox.sh) renders and speaks it locally through its MCP server — **no API keys, no upload, nothing leaves your machine**.

## Why podcast

- **Local and private.** Audio is generated on-device by Voicebox (Kokoro + other open TTS engines). No cloud, no key.
- **Two hosts, not a monologue.** A curious co-host and an expert trade questions and explanations, NotebookLM-style — assigned to two distinct voices.
- **One command.** `/podcast <file|url|topic>` → it reads, scripts, and plays. It also prints the transcript so you can read along.

## Requirements

- **[Claude Code](https://claude.com/claude-code)** — this is a Claude Code skill.
- **[Voicebox](https://voicebox.sh)** installed and running, with its MCP server connected to Claude Code (below). Free, open-source, macOS/Windows.
- **Two voice profiles** in Voicebox (built-in presets or your own clones). Note their names and set them in the `SKILL.md` Configuration block (defaults: `Morgan`, `Scarlett`).

## Installation

```bash
git clone https://github.com/danylo-dudok/agent-skills.git
cp -r agent-skills/skills/podcast ~/.claude/skills/
```

Connect Voicebox's MCP server to Claude Code (with Voicebox running):

```bash
claude mcp add --transport http voicebox http://127.0.0.1:17493/mcp \
  -H "X-Voicebox-Client-Id: claude-code" -s user
```

Confirm both, then restart Claude Code:

```bash
ls ~/.claude/skills/podcast/SKILL.md   # skill present
claude mcp list | grep voicebox        # server connected (✔ when Voicebox is running)
```

> **Update:** `git pull`, then re-run the `cp -r`.
> **Uninstall:** `rm -rf ~/.claude/skills/podcast`.

## Usage

```bash
/podcast raw/some-article.md
/podcast https://example.com/post
/podcast the tradeoffs between Delta Lake and Iceberg
```

It reads the source, writes a ~2–4 minute two-host script, and plays it turn by turn through Voicebox — alternating the two voices — then prints the transcript.

## How it works

1. **Reads the source** — a local file (Read), a URL (WebFetch), or a topic (from context).
2. **Writes the script** — a natural two-host dialogue: a curious host who asks, an expert who explains.
3. **Speaks it** — calls the Voicebox `speak` MCP tool per turn, passing `profile` = host A or host B, so each voice is distinct. Voicebox plays each turn through the speakers automatically.
4. **Prints the transcript** so you can follow along.

## Configuration

Edit the **Configuration** block at the top of `SKILL.md` to match your Voicebox profile names:

```
> HOST_A = `Morgan` (asks) · HOST_B = `Scarlett` (explains)
```

## Notes & limits

- **Playback is live, not a file.** `speak` plays through the speakers but doesn't save an `.mp3`. To keep episodes, Voicebox's REST API (`POST http://127.0.0.1:17493/generate`) renders each turn to a file to concatenate and `afplay` — the skill notes how, on request.
- **Sequential turns** mean small gaps between lines versus NotebookLM's seamless mix. The file-render path above removes them.

## License

[MIT](../../LICENSE) © Danylo Dudok
