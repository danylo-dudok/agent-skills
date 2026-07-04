---
name: "podcast"
description: Generate and play a NotebookLM-style two-host audio podcast from a file, URL, or topic using the Voicebox MCP server. Use when the user runs /podcast or asks to "make a podcast", "turn this into audio", "read this as a conversation", or "give me an audio overview".
---

> **Configuration** — edit the two voice profile names to match yours in Voicebox:
> HOST_A = `Morgan` (curious co-host, asks the questions) · HOST_B = `Scarlett` (the expert, explains).
> Requires the Voicebox app running with its MCP server connected (see README).

# podcast — local NotebookLM-style audio overviews

Turn the given source into a short two-host audio conversation and play it aloud
through the Voicebox `speak` MCP tool. Like NotebookLM's Audio Overview, but local —
no API keys, no upload, no leaving the terminal.

## How you are invoked

The user called you with a file path, a URL, or a topic, e.g.:
- `/podcast raw/some-article.md`
- `/podcast https://example.com/post`
- `/podcast the tradeoffs between Delta Lake and Iceberg`

## Steps

1. **Get the source.**
   - File path → read it with the Read tool.
   - URL → fetch it with WebFetch.
   - No argument → use the current conversation, or ask what the podcast should be about.

2. **Write the script.** Compose a natural ~2–4 minute two-host dialogue:
   - HOST_A is curious and asks the questions; HOST_B is the expert who explains.
   - Conversational and warm — reactions, short asides, "so what that actually means is…".
     Not a lecture, not bullet points read aloud.
   - Open with a one-line hook, cover the 3–5 key ideas with concrete detail, end on the takeaway.
   - Keep each turn to 1–3 sentences so playback flows.

3. **Play it.** For each turn, in order, call the Voicebox `speak` MCP tool with:
   - `text` = just the spoken words for that turn (no "Host A:" prefix)
   - `profile` = HOST_A on HOST_A's turns, HOST_B on HOST_B's turns
   Call them one at a time, in sequence, so the conversation plays in order.
   Voicebox plays each turn through the speakers automatically.

4. After the last turn, print the full transcript to the terminal so the user can read along.

## Notes
- `speak` plays live; it does not save a file. To keep an episode, the Voicebox REST
  API (`POST http://127.0.0.1:17493/generate`) renders a turn to an audio file instead —
  render each turn, then concatenate and `afplay`. Only do this if the user asks to save it.
- Turns play sequentially, so expect small gaps between lines vs. NotebookLM's seamless mix.
