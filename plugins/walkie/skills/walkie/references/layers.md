# Layers, library and apps

## Layers

A walkie is built from separate layers, so each can change without the others:

| Layer | File | Changed by |
|---|---|---|
| Page: the app as Playwright records it, or a Flutter app's simulator screen placed on the stage (`stage.json`), with no overlay on it (the focus zoom is part of it) | `content.mp4` | a new recording |
| Overlay: caption bar, highlight box, spotlight, cursor, cards, veil and their motion, drawn from logged events with the profile's theme | `events.json` + `theme.json` | walkie:rerender |
| Text: captions and card HTML, by id (`step-1`, `caption-2`, `card-1`) | inside `events.json` | walkie:texts, walkie:rerender |
| Voice: one clip per line, placed by the timeline | `voice/` | walkie:revoice |

While recording, the kit draws nothing on the page: it logs every overlay change with its time. The build cuts the recording into `content.mp4`, draws the overlay as transparent frames at full video size (time is exact: every fade is stepped frame by frame), and composes page, overlay and voice into `walkie.mp4`. Timing comes from the recording, so a change to `sequence.json`, the zoom or the video size needs a new recording.

Every heavy step (TTS, recording, building, checking, remixing) waits its turn in a machine-wide queue: a recording is real time and stutters when the CPU is busy.

## Library

Finished walkies go to `~/Library/Application Support/walkie/videos/<repo>--<name>/` (`WALKIE_DATA` moves the whole data folder, `WALKIE_HOME` the library only):

- `walkie.mp4`, and `walkie.previous.mp4` after a revoice or rerender
- the layers: `content.mp4`, `events.json`, `voice/`
- `contact.png`, `trace.zip`, `storyboard.json`, `narration.tsv`, `profile.json`, `demo.spec.ts`
- `backdrops/`: up to 12 stills of the page layer, one per step
- `manifest.json`: repo, branch, commit, PR, profile, clips, `schema_version` (2 = layered), and `usage`: the tokens the agent spent, read from the Claude Code transcripts of every session that worked on it

## What an app may rely on

- `$E/schema/*.schema.json` (JSON Schema 2020-12): `profile`, `theme`, `sequence`, `voice` (all fields optional; defaults equal `$E/profiles/default`), `manifest` and `events`. `x-control`, `x-unit` and `x-step` are hints for an editor. `$E/tests/schema/validate.ts` checks every profile and video against them.
- `walkie profile --list` (name to folder) and `--resolve <name>` (the profile merged over default, paths absolute).
- `walkie preview <profile> [out.html]`: every state of a walkie on a sample page, drawn by the recording's own overlay code. `walkiePreview.render({ theme, sequence }, { fonts, assets, backdrop, only })` or `postMessage({ type: "walkie-profile", profile, fonts, assets, backdrop, only })` redraws it live (`only`: one state, full width); in a frame it posts `walkie-preview-states` to its parent after each render.
- The MCP tools walkie:list, walkie:texts and walkie:profiles return `structuredContent` with an `outputSchema`.
