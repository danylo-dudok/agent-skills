# podcast

> Point it at a file, URL, or topic. `podcast` writes a two-host conversation about it and plays it out loud in two voices — a local, terminal-native take on NotebookLM's Audio Overview.

A Claude Code skill that turns any source into a short **two-host audio podcast** and plays it automatically. Claude writes the dialogue; [Voicebox](https://voicebox.sh) renders it locally and each turn plays through `afplay` — **no API keys, no upload, nothing leaves your machine**.

## Why podcast

- **Local and private.** Audio is generated on-device by Voicebox (Kokoro + other open TTS engines). No cloud, no key.
- **Two hosts, not a monologue.** A curious co-host and an expert trade questions and explanations, NotebookLM-style — assigned to two distinct voices.
- **One command.** `/podcast <file|url|topic>` → it reads, scripts, and plays. It also prints the transcript so you can read along.

## Requirements

- **macOS** with `python3` — playback uses `afplay` (swap for `aplay`/`paplay` on Linux; Voicebox itself is cross-platform).
- **[Claude Code](https://claude.com/claude-code)** installed.

Everything else — the Voicebox app, two voices, and the MCP link — is set up in the walkthrough below.

## Install from scratch

**1. Install the Voicebox app** (the local voice engine).

Download the latest DMG from the **[Voicebox releases page](https://github.com/jamiepine/voicebox/releases/latest)** — `Voicebox_<version>_aarch64.dmg` for Apple Silicon, `Voicebox_<version>_x64.dmg` for Intel — open it, and drag **Voicebox** into **Applications**. It's notarized, so it opens normally (no Gatekeeper override needed).

<details>
<summary>Or install from the terminal (Apple Silicon, needs <code>gh</code>)</summary>

```bash
URL=$(gh api repos/jamiepine/voicebox/releases/latest \
  --jq '.assets[] | select(.name|endswith("aarch64.dmg")) | .browser_download_url')
curl -fL "$URL" -o /tmp/voicebox.dmg
hdiutil attach /tmp/voicebox.dmg -nobrowse
cp -R /Volumes/Voicebox/Voicebox.app /Applications/
hdiutil detach /Volumes/Voicebox
```
</details>

**2. First launch.** Open Voicebox (`open -a Voicebox`) and let it finish downloading its TTS models on first run. Grant any audio permission it asks for.

**3. Create two voice profiles.** In Voicebox, open the **Voices** tab and add two profiles from the bundled presets — e.g. **Bella** and **Alloy**, one per host. Two is the minimum; the skill auto-detects whatever you create.

**4. Connect Voicebox to Claude Code** (MCP), with Voicebox running:

```bash
claude mcp add --transport http voicebox http://127.0.0.1:17493/mcp \
  -H "X-Voicebox-Client-Id: claude-code" -s user
```

**5. Install this skill.**

```bash
git clone https://github.com/danylo-dudok/agent-skills.git
cp -r agent-skills/skills/podcast ~/.claude/skills/
```

**6. Verify, then restart Claude Code.**

```bash
ls ~/.claude/skills/podcast/SKILL.md    # skill present
claude mcp list | grep voicebox         # ✔ connected (Voicebox must be running)
```

Then run a podcast (see [Usage](#usage)).

> **Update:** `git pull`, then re-run the `cp -r`.
> **Uninstall:** `rm -rf ~/.claude/skills/podcast`; `claude mcp remove voicebox` to drop the server.

## Usage

```bash
/podcast raw/some-article.md
/podcast https://example.com/post
/podcast the tradeoffs between Delta Lake and Iceberg
```

It reads the source, writes a ~2–4 minute two-host script, and plays it turn by turn through Voicebox — alternating the two voices — then prints the transcript.

## How it works

1. **Reads the source** — a local file (Read), a URL (WebFetch), or a topic (from context).
2. **Writes the whole script first** — the complete two-host dialogue in one pass (curious host asks, expert explains), printed up front so you see the full episode before any audio.
3. **Then voices it** — for each turn, `scripts/say.sh` renders the audio via Voicebox (`POST /generate`, `engine=kokoro`), downloads the clip (`GET /audio/{id}`), and plays it with `afplay` (which blocks, so turns stay in order). The live `speak` tool isn't used — it's async, and overlapping turns collide.

## Configuration

Edit the **Configuration** block at the top of `SKILL.md` to match your Voicebox profile names:

```
> HOST_A = `Bella` (asks) · HOST_B = `Alloy` (explains)
```

## Notes & limits

- **Render-to-file, not live playback.** Each turn renders to `/tmp/podcast_<id>.wav` and plays via `afplay` (which blocks → clean ordered playback, no overlap). No `ffmpeg` needed. Requires macOS + `python3`.
- **Engine matters.** `/generate` defaults to the qwen engine; `say.sh` forces `engine=kokoro` for the Bella/Alloy presets (else HTTP 400). Set `VOICEBOX_ENGINE` for other voices.
- **Keep one file (optional).** Clips are deleted after playing; skip the cleanup and `ffmpeg`-concat them for a single saveable episode. Small gaps between turns are render latency, not playback.

## License

[MIT](../../LICENSE) © Danylo Dudok
