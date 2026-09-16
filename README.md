# skills

Agent skills for an [Omarchy](https://omarchy.org/) desktop — Arch Linux,
Hyprland, `omarchy-shell`.

These skills shell out to `omarchy` commands and read files under
`/usr/share/omarchy/`. **They need Omarchy to be installed**; on any other
distro they will not work — except `discord-notify`, which only borrows
Omarchy's agent icons and degrades to no icon without them.

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
| [`discord-notify`](skills/discord-notify) | The same, over a Discord webhook, so a session can reach you when you are away from the machine. |

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

## discord-notify

`omarchy-notify` reaches you at the desk. This one reaches you anywhere — it
posts to a Discord webhook, so a session that finishes at 2am lands on your
phone. Same bar for interrupting, same headline-and-description shape.

Each message is an embed branded with the agent's own icon, coloured by
urgency, and footed with the conversation it came from and the branch it was
on, so a wall of notifications from parallel sessions stays readable:

```
┌─────────────────────────────────────────────┐
│ 🟠 Claude                                   │
│ alcodefi · migration failed                 │
│ Rolled back at step 3. Needs a decision on  │
│ the `orders` column before I retry.         │
│ Discord notify skill · main    Today 02:14  │
└─────────────────────────────────────────────┘
```

### Setup

Create a webhook in Discord — **Channel → Edit Channel → Integrations →
Webhooks → New Webhook → Copy Webhook URL** — then put it somewhere the script
will find it:

```bash
mkdir -p ~/.config/discord-notify
echo 'https://discord.com/api/webhooks/...' > ~/.config/discord-notify/webhook
chmod 600 ~/.config/discord-notify/webhook
```

`DISCORD_WEBHOOK_URL` in the environment works too and wins over the file, and
`--webhook URL` wins over both — useful for routing a particular project to its
own channel.

To have `critical` sends actually push to your phone, set a mention. Right-click
yourself in Discord → **Copy User ID** (Developer Mode must be on):

```bash
export DISCORD_NOTIFY_MENTION='<@123456789012345678>'   # or '<@&roleid>'
```

Only `critical` uses it. `normal` and `low` arrive silently.

### What it fills in for you

The agent writes the headline and description. Everything else the script works
out:

| Field | Where it comes from |
| --- | --- |
| Icon + name | The agent running it, identified by walking up the process tree |
| Colour | The `-u` urgency — red, blurple, grey |
| Footer | The conversation title, then the git branch |
| Timestamp | The send |

The conversation title is Claude Code's own: the AI-written title from the
session transcript, falling back to the derived session name, then to the
working directory's name. Other agents land on the directory name.

### Icons

Discord cannot render SVG, so an SVG icon is rasterised to PNG once with
`rsvg-convert` (or ImageMagick) and cached in `~/.cache/discord-notify/`. PNGs
are preferred where both exist. Lookup order otherwise matches `omarchy-notify`:

| Location | Purpose |
| --- | --- |
| `~/.config/omarchy/agent-icons/<agent>.{png,svg}` | Yours. Overrides everything. |
| `skills/discord-notify/icons/<agent>.{png,svg}` | Shipped with the skill. |
| `/usr/share/omarchy/shell/plugins/agents/assets/` | Omarchy's own. |

With no icon anywhere the embed simply goes out without one.

### Using it directly

```bash
skills/discord-notify/notify -u critical "deploy · rolled back" "Staging is green, prod reverted to 1.4.2."
```

Needs `curl` and `jq`.

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
