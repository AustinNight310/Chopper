# Chopper

A session inventory skill for [Claude Code](https://claude.com/claude-code). It reads the current conversation back and sorts every thread into two lists:

- **LIVE** — decisions still binding, files mid-edit, open questions
- **DONE** — dead ends, superseded plans, resolved bugs, tool output already acted on

One audit, several uses: resuming after a break, handing off, writing up what happened, or aiming a `/compact` before you run it.

```
LIVE
- Decided on JWT refresh tokens
- auth/session.ts — mid-refactor
- Open question: token TTL
- Next: wire the refresh endpoint

DONE
- Cookie-vs-JWT debate (settled)
- Redis token store prototype (abandoned)
- 400-line read of node_modules/jose
- TypeError in test setup (fixed)

Run: /compact Keep: decided on JWT refresh tokens; auth/session.ts mid-refactor;
open question on token TTL. Drop: the cookie-vs-JWT debate, the abandoned Redis
prototype, the jose source read, the resolved TypeError.
```

## Install

```
/plugin marketplace add AustinNight310/Chopper
/plugin install chopper@chopper
```

Or drop `skills/chopper/` into `~/.claude/skills/` and skip the plugin machinery.

## Use

Invoke at a boundary — `/chopper`, or just ask "what's still live here?"

Good moments:

| Moment | What you get |
|---|---|
| Resuming yesterday's session | The LIVE list *is* "where were we" |
| Handing off to another session or person | LIVE plus the immediate next step |
| Before a `/compact` you were already running | A focus string that names real files and decisions |
| A session that has clearly gone sideways | Often just `Run: /clear`, with a reason |

## When not to use it

Short sessions, focused sessions, or any time you already know what you're doing next. You have information the transcript doesn't — you'll write a better summary yourself, faster. Chopper is built to say "nothing to cut here" and stop.

It also earns its keep on *cluttered* sessions, not merely large ones. A 30 MB session of dense, relevant work needs nothing; a 3 MB session of four abandoned approaches needs it badly. That's why there's no size-triggered hook in this repo — transcript bytes measure "big", not "cluttered", and firing on size gets it wrong in both directions.

## What it can't do

`/compact` and `/clear` are harness commands. A skill is text the model reads; it cannot invoke them. Chopper does the reading and the sorting, then hands you the finished command to run.

## Does it save tokens?

Usually no, and it's worth being straight about that. `/compact` with a focus string doesn't produce a *smaller* summary than compacting blind — it produces a better-aimed one. Same token count, different contents.

The exception is the `/clear` verdict: clearing a bloated session costs nothing, while compacting it pays a full-context read.

The real value is rework avoidance. Compaction silently drops the one decision you needed, you re-derive it, and that costs far more than the audit did. That's insurance, not savings.

## License

MIT
