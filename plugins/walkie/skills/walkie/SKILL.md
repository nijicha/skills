---
name: walkie
description: Makes walkies, narrated videos of a code change. Records the app with Playwright (or a Flutter app on an iOS Simulator or Android emulator), adds a Thai voice-over, numbered captions, highlights and cards in a per-team look, and builds an MP4 to attach to a PR. Changes the narration, the text or the look of a finished video without recording again. Use when the user asks for a walkie, a walkthrough or demo video, a narrated screen recording, or a video for a PR or ticket.
allowed-tools: Bash(walkie *)
---

# Walkie

A **walkie** is a slow, narrated video of one change, for people outside engineering (support, operations, organizers). Each **scene** shows one step: a numbered caption names it, a highlight box marks the element, a cursor clicks it, and one voice **clip** explains it. An intro card opens the walkie and a summary card closes it.

The engine is a separate install, the `walkie` command ([nijicha/walkie-engine](https://github.com/nijicha/walkie-engine)). `$E` in the reference files is the folder `walkie path` prints. The MCP server `walkie` wraps the engine; use its tools.

## Before the first walkie

If the walkie MCP tools are missing, or `walkie version` fails, the engine is not installed. Tell the user to run, once per machine:

```bash
brew install nijicha/tap/walkie
walkie setup
```

Then restart Claude Code. Do not try to install it yourself.

## Run it in a fresh context

Every API call re-reads the whole conversation, so a walkie made at the end of a long session costs about twice what it costs in a fresh one.

- **Claude Code:** hand the work to the `walkie` subagent with a short brief: the ticket or PR, what changed and why, the screens or endpoints to show, the scenes in mind, how to start the app and its URL. It stops at each checkpoint; show the user what it returns, then continue the same subagent (SendMessage) with their answer. Do not also do its work.
- **Other agents:** start a new session with the same brief.
- **A break of an hour or more** expires the prompt cache: finish first, or compact before stepping away.

## Workflow

Copy this checklist and tick it off:

```
Walkie progress:
- [ ] 1. walkie:start (repo, name) and read the profile's NOTES.md it returns
- [ ] 2. Write the storyboard: one scene per step, intro first, summary last
- [ ] 3. walkie:storyboard until no line is flagged FAST, CHOPPY or DROPPED
- [ ] 4. CHECKPOINT 1: show the storyboard table, wait for approval
- [ ] 5. Write demo.spec.ts (Flutter: demo_test.dart) from the template, with an expect in every scene
- [ ] 6. Start the app as NOTES.md says (Flutter: a simulator or emulator instead)
- [ ] 7. walkie:record, then read the contact sheet it returns
- [ ] 8. CHECKPOINT 2: show the contact sheet and the MP4, wait for approval
- [ ] 9. Hand over: MP4 link, length, scene list, bugs found; stop the servers you started
```

**Step 2.** Ask only what the change and the code do not answer: the audience, and which PR or ticket. Each row: action, element to highlight, caption (spelled as the UI shows it), narration (Thai, written for the ear, see [pronunciation](references/pronunciation.md)). When the change is a guard, show the error path before the fix.

**Step 3.** The tool returns each line's length, pace and phonemes. Fix only the flagged lines: add or remove spaces, respell, or add a lexicon entry (walkie:lexicon, test it with walkie:voice_preview). Call it again.

**Step 5.** Follow the profile's NOTES.md and the [kit reference](references/kit.md); for a Flutter app (walkie:start says `Runner: flutter`), the [Flutter reference](references/flutter.md) instead. Import only from `./kit`. The `expect` in each scene makes a broken feature fail the recording instead of producing a wrong video: when it fails, the change may have a real bug. Fix the code, not the walkie, and tell the user.

**Step 7.** walkie:record waits its turn in the machine-wide queue, records, builds the video from its layers, and checks it. Pass `base_url` when the app is not at the profile's address. Done when every clip's frames show its caption and its highlight on the right element, and there is no FLASH.

## Changes after checkpoint 2

A walkie is built from separate layers ([layers](references/layers.md)). Change only the layer the request touches:

| The user wants | Do | Not |
|---|---|---|
| A narration line said differently | walkie:revoice those clips | record again |
| A caption or card worded differently | walkie:texts, then walkie:rerender with `text` | record again |
| Other colors, fonts, sizes, card style or logo | change the profile, then walkie:rerender | record again |
| A different step, order, page, pause, zoom or video size | edit the spec (and storyboard), walkie:record | |

walkie:revoice refuses a clip longer than its gap: shorten the line; record again only if it cannot be shorter. Show the new contact sheet as at checkpoint 2.

## Other tools

walkie:pr_text (PR description Markdown, copied to the clipboard; the MP4 is dragged into the PR by hand, GitHub has no upload API), walkie:profiles, walkie:list, walkie:delete and walkie:purge (ask the user first).

## References

- [Profiles](references/profiles.md): where a team's look, voice and repo notes live; making a new profile ([setup interview](references/setup-interview.md))
- [Kit](references/kit.md): the `Demo` API a spec uses, and how the video is framed
- [Flutter](references/flutter.md): Flutter apps on an iOS Simulator or Android emulator: the Dart kit, zoom, recording
- [Pronunciation](references/pronunciation.md): writing Thai narration for the voice
- [Layers](references/layers.md): page, overlay, text and voice; the library; what an app may rely on
- [Without MCP](references/without-mcp.md): setup, and every step as a shell command
