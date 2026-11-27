# Kit

For a Flutter app the spec is `demo_test.dart` and the kit is Dart: see [Flutter](flutter.md).

A spec (`demo.spec.ts`) imports `test`, `expect`, `Demo` and the profile's helpers (as `profile`) from `./kit`, which walkie:start writes next to it. Never import from `@playwright/test`: the engine records with its own Playwright, and the repo's copy must not load next to it. For the same reason a spec cannot use the repo's page objects; the profile's helpers cover login and seed data.

`const demo = await Demo.create(page)` after login. It reads the walkie folder from `process.env.DEMO_DIR` (never `__dirname`, which an ES module repo does not have).

## Calls

| Call | Does |
|---|---|
| `intro(html, "n0")` | Intro card (`.tag` with one `<span>` per item, `h1`, `p`), time zero, clip `n0`, fade out. With `intro` off, only starts the clock |
| `chapter(html, clip?)` | Short card between parts ("ก่อนแก้" / "หลังแก้"). Only when `chapter` is on |
| `summary(html, clip)` | Closing card: `ul > li` (the card draws the check marks; no emoji), `li.warn` for a caution. Only when `summary` is on |
| `outro(clip?)` | The profile's `outro_html` card. Only when `outro` is on |
| `stepCaption(html)` | Caption with the next step number. `<b>` is the emphasis color |
| `caption(html)` | Caption without a number. `""` hides it |
| `highlight(locator)` | Highlight box around one field or button, never a whole form. `null` hides it |
| `highlight(locator, { spotlight: true })` | Also dims the rest of the page. For text-heavy pages |
| `highlight(locator, { zoom: true })` | Also zooms the page and brings the element to the middle (`zoom: 1.4` for a scale; capped so the element fits). For busy pages. The next `highlight` zooms back out first |
| `say(name)` / `untilQuiet()` | Waits for the previous clip, then starts clip `name` / waits for the current clip to end |
| `goto(url)` / `navigate(async () => …)` | Change page behind a veil: the page fades to the card color, loads, and fades in. Use for every page change after the intro (a click that loads a page goes in `navigate`), or a dark page cuts to a light one (a FLASH) |
| `scrollTo` / `moveTo` / `click(locator)` | Smooth scroll / move the cursor / cursor then click |
| `pause(ms)` | Waits (default 2.2 s) |
| `finish()` | Writes `voice/timeline.json` and `events.json`. Call it last |

Each scene: `highlight`, `stepCaption`, `say`, act, then `expect` the result.

A caption or a highlight goes away when the page navigates (`navigate` clears them under the veil). Set it again after.

Captions and card text break lines only at the spaces written (Chrome's Thai dictionary splits loanwords mid-word, ริ / สต์แบนด์), so put a space where a long title may wrap.

## Framing

The page lays out at the video size divided by `zoom` (853 × 480 CSS pixels for 1280 × 720 at 1.5), like a browser at 150 %. The build scales the page up and draws the overlay at full size, so text stays sharp. `intro` (or `start()`) shows a magenta square in the corner for 0.4 s; the build cuts the recording right after it, so the login and the square never show.

## Smoke test

After changing the kit, run `walkie smoke` from any folder. It records `$E/tests/smoke/` (a static page, no app), builds the video, writes `contact.png` in `$TMPDIR/walkie-smoke`, and fails on a FLASH.
