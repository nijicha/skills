# nijicha/skills

Claude Code plugins by nijicha, in one marketplace.

Docs: [nijicha.github.io/skills](https://nijicha.github.io/skills)

```
/plugin install walkie --marketplace nijicha/skills
```

On Claude Code older than 2.1.275, add the marketplace first:

```
/plugin marketplace add nijicha/skills
/plugin install walkie@nijicha-skills
```

## Plugins

| Plugin | What it does | Needs |
|---|---|---|
| [walkie](plugins/walkie) | Makes walkies: slow, narrated videos of a code change for people outside engineering, with a Thai voice-over, captions and highlights, ready to attach to a PR. Skill, Sonnet subagent and MCP server. | The [walkie engine](https://github.com/nijicha/walkie-engine): `brew install nijicha/tap/walkie && walkie setup` (macOS) |

## Issues

Report a problem with a plugin here. For the walkie engine itself (recording, voice, video), open an issue in [nijicha/walkie-engine](https://github.com/nijicha/walkie-engine/issues).

## License

MIT
