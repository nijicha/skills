# Profiles

A **profile** holds everything that belongs to one team: colors, fonts, layout, which cards a walkie uses, the voice, the lexicon and the repo's notes. The built-in `$E/profiles/default/` is the base: each file of a profile is merged over the same file in `default`, so a profile lists only what it changes. JSON Schemas for every file are in `$E/schema/`.

A team keeps its profile in its own repo and installs it once per machine:

```bash
git clone <profile repo> ~/.config/walkie/profiles/<name>
```

Profiles are found, first match wins, in each folder of `WALKIE_PROFILES` (colon-separated), `~/.config/walkie/profiles`, then `$E/profiles/`. A profile's name is the `name` in its `profile.json`. walkie:start picks one by git remote, or takes the one named.

| File | Holds |
|---|---|
| `profile.json` | `match` (git remote globs, `host/owner/name`, that pick this profile), optional `allow` (the only remotes that may use it), `work_dir` (must be gitignored), `template`, `record.base_url` (overridden for one recording by walkie:record's `base_url`); for Flutter apps `record.runner`, `app_dir`, `device` and `flutter` (see [Flutter](flutter.md)) |
| `theme.json` | `colors`, `fonts`, `hide` (selectors of dev-only UI to keep out of the video), `layout` (video size, zoom, caption, card and highlight sizes, fades), `cards` (`all`, then per kind `intro` / `chapter` / `summary` / `outro`: alignment, padding, tag band, logo, background). Sizes are pixels of the final video; image paths are relative to the profile |
| `sequence.json` | `intro`, `chapter`, `summary`, `outro` on or off, `step_numbers` |
| `voice.json` | `engine` (`makham`: Thai), `model` (`makham_female`; `paradee` is faster but buzzy, for drafts), `speed`, `phrase_gap`, `max_rate`, `eq` (an ffmpeg filter on each clip), `breath` (`cut`, `soften` by `breath_db`, or `keep`), `lexicon` |
| `NOTES.md` | How this repo starts the app, seeds data and logs in, and its gotchas. Read it before writing the spec |
| `template.spec.ts` | The spec to copy |
| `helpers.ts` | Optional: the team's login and seed-data helpers, available as `profile` in `./kit`. Import Playwright for types only |

`default` uses the Prompt font. Every profile gets CaskaydiaCove Nerd Font for `<code>`, `.mono` and symbols the sans font lacks.

When no profile matches the repo and the user wants their own look, run the [setup interview](setup-interview.md).
