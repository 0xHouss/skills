---
name: discord-notify
description: Send the user a Discord notification from this agent session, to reach them when they are away from the machine. Use when the user asks to be notified on Discord ("notify me on discord", "ping me on discord when you finish"), and when a session needs to reach them off-machine — blocked waiting on them, a discovery that changes their plans, or long-running work reaching a terminal state.
---

# Discord notify

The user runs several agent sessions at once and is not always at the machine.
A Discord message is how one session reaches them when a desktop notification
would not — away from the desk, on their phone, elsewhere.

## Send one

Run the `notify` script sitting next to this file — every agent installs skills
somewhere different, so resolve it from this skill's own directory rather than
from a fixed path:

```bash
"<this skill's directory>/notify" [-u low|normal|critical] "<headline>" "<description>"
```

The script works out which agent is running it and which conversation it came
from, and brands the message with that agent's icon and this conversation's
title. Pass the text and, when it matters, the urgency — nothing else.

If it exits non-zero it prints why. A missing webhook is a setup problem the
user has to fix; say so in your reply rather than retrying.

## The bar

A notification is an interruption: it pulls the user out of whatever else they are doing. One earns that when **it changes what the user does next** — they come back, answer something, fix something, or stop waiting on you.

Everything else belongs in the reply they are already going to read. Progress updates, steps finishing inside a task you are still running, a recap of what you just did, a second notification about a stopping point you already announced — the reply carries all of it at no cost to their attention.

Discord is the louder channel of the two: it follows the user off the machine
and onto their phone. If they are plainly at the desk and a desktop
notification would do, prefer `omarchy-notify`. On a close call, stay quiet. A
session that pings only when it matters is one the user keeps trusting.

## What earns one

**You are blocked and waiting on them.** A decision between real alternatives, a secret or credential, an interactive login only they can complete, permission for something irreversible, a physical action like plugging in a device or connecting a VPN, or a tool denial you cannot route around.

**You found something that changes their plans.** A leaked secret, a security hole, a data-loss risk, a production bug you stumbled into. The premise of the task turning out to be wrong — already done, impossible, or destructive to carry out. A job burning money or quota far past expectation. The ground moving underneath: someone else pushed, the branch changed.

**Long work reached a terminal state.** It succeeded, it failed and will not proceed, or it finished with part of the scope skipped or unverified. Background jobs, subagents, and watched external things — a CI run going red, a deploy landing — count here, because the user cannot see those at all.

**They asked.** "Notify me on Discord", "ping me on Discord when done", or a reminder they set up. Send it even for work that would not otherwise clear the bar.

## Urgency

| Level | Colour | For |
|---|---|---|
| `critical` | red | Blocked and waiting, the task failed, or harm is ongoing |
| `normal` | blurple | Work finished, or a problem found that is not actively costing them |
| `low` | grey | A milestone worth recording, not worth interrupting for |

`critical` also pings the user directly, if they configured a mention. That is
the whole reason the level exists — do not reach for it to add emphasis.

## Write it for a glance

The user reads this on their phone, out of context, with no idea which of their
sessions sent it. The footer already carries the conversation and branch, so
spend the text on what happened.

- **Headline** — the project, then the outcome: `omarchy-skill · 14 tests passing`, `alcodefi · migration failed`.
- **Description** — one line on what happened, and what they have to do next if anything. Discord renders markdown here, so a backticked path or command is worth the characters.
