# skills

Agent skills for an [Omarchy](https://omarchy.org/) desktop — Arch Linux,
Hyprland, `omarchy-shell`.

These skills shell out to `omarchy` commands and read files under
`/usr/share/omarchy/`. **They need Omarchy to be installed**; on any other
distro they will not work.

## Install

```bash
npx skills add 0xHouss/skills
```

That installs to whichever agents you pick — Claude Code, Codex, OpenCode,
Cursor, Gemini CLI and [70+ more](https://github.com/vercel-labs/skills).
Pick one skill, or target one agent:

```bash
npx skills add 0xHouss/skills --skill omarchy-notify
npx skills add 0xHouss/skills -a claude-code -g
```

## Skills

| Skill | What it does |
| --- | --- |
| [`omarchy-notify`](skills/omarchy-notify) | Lets an agent session reach you by desktop notification when you are not watching it, branded with that agent's own icon. |

## omarchy-notify

You run several agent sessions at once and cannot watch them all. This skill
gives a session a way to take your attention back — when it finishes long work,
when it is blocked waiting on you, or when it hits something that changes what
you should do next. The skill carries a deliberately high bar for interrupting,
so sessions stay quiet until it matters.

The agent writes the headline and description. It does not pick the icon: the
`notify` script identifies which agent is running it by walking up the process
tree, and brands the notification itself, so a Codex notification looks like
Codex and a Claude one looks like Claude.

### Icons

Icons are resolved in this order, first match wins:

| Location | Purpose |
| --- | --- |
| `~/.config/omarchy/agent-icons/<agent>.{svg,png}` | Yours. Overrides everything. |
| `skills/omarchy-notify/icons/<agent>.{svg,png}` | Shipped with the skill. |
| `/usr/share/omarchy/shell/plugins/agents/assets/` | Omarchy's own. |

Omarchy ships marks for **claude** and **codex**. Any other agent falls back to
a robot glyph until an icon exists for it — drop `<agent>.svg` into either
writable directory to add one. The names the script recognises are `claude`,
`codex`, `opencode`, `gemini`, `copilot`, `crush`, `grok`, `pi` and `omp`.

### Using it directly

The script is useful on its own, agent or not:

```bash
skills/omarchy-notify/notify -u critical "deploy · rolled back" "Staging is green, prod reverted to 1.4.2."
```

## Repo layout

One directory per skill under `skills/`:

```
skills/
└── <skill-name>/
    └── SKILL.md      # name + description frontmatter, then the instructions
```

Adding a skill means adding its directory and a row in the table above.

## License

MIT — see [LICENSE](LICENSE).
