# Setup interview: make a profile

Run this when walkie:start says no profile matched the repo and the user wants their own look rather than `default`. It produces a profile folder, best kept in the team's own repo and cloned into `~/.config/walkie/profiles/<name>/`.

Ask one question at a time. Give your recommended answer with each question, and wait for the user's answer before the next one. Look facts up instead of asking: brand colors and fonts from the repo's CSS or tokens, the git remote, how the app starts and logs in. Write nothing until the user confirms the summary at the end.

## Questions, in order

1. **Name and match.** Profile name, and the `match` globs (`github.com/<owner>/*`). Recommend matching the owner, so every repo of that team picks it.
2. **Allowlist.** Should the profile refuse other repos (`allow`, exact remotes)? Recommend it only when the brand must not leak into other work.
3. **Audience and screen.** Who watches, and at what size? Recommend: inline in a PR or chat at 600–800 px, so `zoom` 1.5 and a 32 px caption at the bottom.
4. **Brand.** Primary color (caption bar, card text), emphasis color (caption `<b>`), highlight color (the box, red by default). Read them from the repo first and confirm.
5. **Fonts.** The brand's sans font, medium and bold, copied to the profile's `fonts/` (woff2). Check its license allows that. Without one, keep Prompt. Monospace and symbols always use CaskaydiaCove Nerd Font.
6. **Sequence.** Which of `intro`, `chapter`, `summary`, `outro` are on, and step numbers in captions. Recommend intro, summary and step numbers on; chapter off (turn on per video for before/after); outro off.
7. **Cards.** Light cards (white, primary text) keep brightness steady next to light pages; dark cards suit dark apps. Design two or three card variants, render each with `walkie preview <profile>`, and let the user pick.
8. **Voice.** Language and engine (`makham` is Thai only). Speed and pause defaults. Collect product names the TTS gets wrong and add them to the lexicon.
9. **Repo notes.** How to start the app, its URL (`record.base_url`), seed data and log in. These go to `NOTES.md`; login and seeding code goes to `helpers.ts` (Playwright types only, no runtime import).

## Finish

Summarize every answer as a list and ask the user to confirm. Then write `profile.json`, `theme.json`, `sequence.json`, `voice.json` and `NOTES.md` with only the values that differ from `$E/profiles/default`. Record a short sample (intro, one step, summary), read its contact sheet, check the frames yourself (contrast, text that wraps or is cut, spacing, a highlight on the wrong element), fix what you find, and show the contact sheet before calling the profile done.
