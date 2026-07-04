---
name: "podcast"
description: Generate and play a NotebookLM-style two-host audio podcast from a file, URL, or topic using the Voicebox MCP server. Use when the user runs /podcast or asks to "make a podcast", "turn this into audio", "read this as a conversation", or "give me an audio overview".
---

> **Configuration (optional)** — preferred voice profile names, if you have them:
> HOST_A = `Bella` (curious co-host, asks) · HOST_B = `Alloy` (expert, explains).
> If these don't exist, the skill auto-picks two of your Voicebox profiles.
> Requires the Voicebox app running with its MCP server connected (see README).

# podcast — local NotebookLM-style audio overviews

Turn the given source into a short two-host audio conversation and play it aloud
through the Voicebox `speak` MCP tool. Like NotebookLM's Audio Overview, but local —
no API keys, no upload, no leaving the terminal.

The flow is two phases: **write the entire script first, then voice it.** Never
interleave writing and speaking — compose the whole episode up front so it's coherent,
then hand the finished script to the voice tool.

## How you are invoked

The user called you with a file path, a URL, or a topic, e.g.:
- `/podcast raw/some-article.md`
- `/podcast https://example.com/post`
- `/podcast the tradeoffs between Delta Lake and Iceberg`

## Steps

1. **Pick the two voices.** Call the Voicebox `list_profiles` MCP tool.
   - Use the Configuration names (HOST_A / HOST_B) if they appear; otherwise the first
     two distinct profiles — and tell the user which two you're using.
   - Fewer than two profiles? Stop and ask the user to create two in Voicebox → Voices, then rerun.

2. **Get the source.**
   - File path → read it with the Read tool.
   - URL → fetch it with WebFetch.
   - No argument → use the current conversation, or ask what the podcast should be about.

3. **Write the whole script first — in one pass.** Before voicing anything, compose the
   ENTIRE ~2–4 minute two-host dialogue as one ordered list of turns (each: speaker + text):
   - HOST_A is curious and asks the questions; HOST_B is the expert who explains.
   - Conversational and warm — reactions, short asides, "so what that actually means is…".
     Not a lecture, not bullet points read aloud.
   - Open with a one-line hook, cover the 3–5 key ideas with concrete detail, end on the takeaway.
   - Keep each turn to 1–3 sentences so playback flows.
   Print the finished transcript so the user sees the whole episode up front.

4. **Then hand the finished script to the voice tool.** Voice it verbatim — do not rewrite
   or add lines during this phase. For each turn, in order, call `speak` with:
   - `text` = that turn's words (no "Host A:" prefix)
   - `profile` = the HOST_A voice on HOST_A's turns, the HOST_B voice on HOST_B's turns
   `speak` plays one turn at a time and auto-saves it to Voicebox's History. Call the next
   turn only after the previous `speak` call returns, so turns play in order. (If turns
   overlap because `speak` is async, poll `GET http://127.0.0.1:17493/generate/{id}/status`
   with the id `speak` returns, and wait for `done` before the next turn.)

## Notes
- **One seamless file (optional).** `speak` plays live and saves each turn separately. To
  produce a single saveable episode instead, render each turn with `POST
  http://127.0.0.1:17493/generate` to an audio file, concatenate with `ffmpeg`, then
  `afplay`. Only do this if the user asks to save the episode as one file.
