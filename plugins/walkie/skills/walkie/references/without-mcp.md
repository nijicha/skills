# Without MCP: setup and shell commands

## Setup

Once per machine:

```bash
brew install nijicha/tap/walkie
walkie setup
```

The Homebrew package already holds the engine's Node packages and its tools (`$E/bin/wt`); it needs only node and ffmpeg, which brew installs. `walkie setup` installs the engine's own Playwright Chromium and downloads the Thai voice model and dictionaries to `~/.cache/walkie/tts`; `walkie setup en` also downloads the English one (Kokoro, 92 MB). The engine records with its own Playwright and Chromium, whatever the repo has.

From a source checkout of the engine (for working on it), `walkie setup` also needs mise and pnpm: it installs the Node packages and builds `bin/wt` with Rust from mise.

## Steps by hand

Run from the repo root with `S=$E/scripts`, `WT=$E/bin/wt` and `W` set to the profile's `work_dir` (`tmp/demo` unless the profile says otherwise; it must be gitignored). Wrap each heavy step in the queue: `node $S/queue.ts <label> -- <command>`.

1. Pick the profile; it prints which and why:

   ```bash
   $WT profile $W/<name>        # DEMO_PROFILE=<profile> to choose
   ```

2. Write `$W/<name>/narration.tsv`, one line per clip (`n0<TAB>text`, `n0` for the intro), and voice it:

   ```bash
   $WT tts $W/<name> < $W/<name>/narration.tsv
   ```

3. Copy the template that `wt profile` printed to `$W/<name>/demo.spec.ts` and write the scenes.

4. Start the app as `NOTES.md` says, then record:

   ```bash
   DEMO_DIR=$W/<name> $E/node_modules/.bin/playwright test -c $S/demo.config.ts
   ```

5. Build from the layers and check:

   ```bash
   D=$W/<name>; R=$(command ls -d $D/results/*/ | head -1)
   node --no-warnings $S/build.ts $D $R/video.webm
   $WT frames $D/walkie.mp4 $D $D/contact.png
   ```

   Read `contact.png`. A `FLASH` comes from a cut between dark and light in one frame: fade it instead.

## Tests

From a source checkout of the engine:

```bash
cd $E && bin/walkie test   # wt's tests, schemas, smoke recording
cd $E && pnpm typecheck     # the TypeScript
cd $E && pnpm test:mcp      # every MCP tool, end to end
```
