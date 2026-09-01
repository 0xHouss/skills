---
name: omarchy-notify
description: Send the user a desktop notification from this agent session, to reach them when they are not watching it. Use when the user asks to be pinged ("notify me", "tell me when you finish"), when you are blocked waiting on them, when you hit a problem or discovery that changes what they should do next, or when long-running work succeeds, fails, or finishes with caveats.
---

# Omarchy notify

The user runs several agent sessions at once and cannot watch them all. A notification is how one session reaches them when they are not looking at it.

## Send one

Run the `notify` script sitting next to this file — every agent installs skills
somewhere different, so resolve it from this skill's own directory rather than
from a fixed path:

```bash
"<this skill's directory>/notify" [-u low|normal|critical] "<headline>" "<description>"
```

The script works out which agent is running it and brands the notification with
that agent's icon. Pass the text and, when it matters, the urgency — nothing else.

## The bar

A notification is an interruption: it pulls the user out of whatever else they are doing. One earns that when **it changes what the user does next** — they come back, answer something, fix something, or stop waiting on you.

Everything else belongs in the reply they are already going to read. Progress updates, steps finishing inside a task you are still running, a recap of what you just did, a second notification about a stopping point you already announced — the reply carries all of it at no cost to their attention.

On a close call, stay quiet. A session that pings only when it matters is one the user keeps trusting.

## What earns one

**You are blocked and waiting on them.** A decision between real alternatives, a secret or credential, an interactive login only they can complete, permission for something irreversible, a physical action like plugging in a device or connecting a VPN, or a tool denial you cannot route around.

**You found something that changes their plans.** A leaked secret, a security hole, a data-loss risk, a production bug you stumbled into. The premise of the task turning out to be wrong — already done, impossible, or destructive to carry out. A job burning money or quota far past expectation. The ground moving underneath: someone else pushed, the branch changed.

**Long work reached a terminal state.** It succeeded, it failed and will not proceed, or it finished with part of the scope skipped or unverified. Background jobs, subagents, and watched external things — a CI run going red, a deploy landing — count here, because the user cannot see those at all.

**They asked.** "Notify me", "ping me when done", "tell me when you finish", or a reminder they set up. Send it even for work that would not otherwise clear the bar.

## Urgency

| Level | For |
|---|---|
| `critical` | Blocked and waiting, the task failed, or harm is ongoing |
| `normal` | Work finished, or a problem found that is not actively costing them |
| `low` | A milestone worth recording, not worth interrupting for |

## Write it for a glance

The user reads this from across the room, out of context, with no idea which of their sessions sent it.

- **Headline** — the project, then the outcome: `omarchy-skill · 14 tests passing`, `alcodefi · migration failed`.
- **Description** — one line on what happened, and what they have to do next if anything.
