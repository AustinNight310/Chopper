---
name: Chopper
description: Use when you need to know what is still live in the current session — resuming work after a break, handing off to another session or teammate, writing up what happened, deciding whether to keep going or start fresh, or aiming a /compact before running it. Trigger on "chopper", "what's live", "what's still relevant", "session inventory", "audit context", "what can we clear", "trim the context", "where were we".
---

# Chopper

## Overview
Takes inventory of the current session: reads the conversation back and sorts every thread into what still does work (KEEP) and what is finished (CUT). One audit, several uses — handoff, resumption, write-up, and aiming a context trim all need the same answer.

Chopper reports. It cannot run `/compact` or `/clear` — those are harness commands a skill cannot invoke.

## When to use
Invoke at a boundary, not on a timer:

- **Resuming** a session after a break — "where were we" is the KEEP list.
- **Handing off** to another session, agent, or person.
- **Phase change** — before starting a long new stretch, so it isn't built on stale context.
- **Before a `/compact`** you were already going to run — near-free at that point, and strictly better than compacting blind.
- **Writing up** what happened: a PR description, a status note, a decision record.

**Don't** invoke on a short or focused session, or when you already know what you're doing next. You have information the transcript doesn't; you will write a better summary faster than this audit takes to produce. Say so and stop.

## The sort
Walk the conversation top to bottom. Tag each distinct thread once:

| | |
|---|---|
| **KEEP** | Decisions still binding on later work · files actively being edited · unresolved questions · anything referenced in the last few turns |
| **CUT** | Dead-end explorations · superseded plans · resolved bugs and their whole chase · raw tool output (file reads, searches, command logs) whose conclusion is already captured in prose |

One question decides it: **does anything still depend on this?** If yes it survives. If its conclusion already lives somewhere shorter, it goes.

When genuinely unsure, KEEP. A wrong CUT destroys the only record of something; a wrong KEEP costs a few tokens.

## Output
Always produce the inventory:

```
LIVE
- <decision, file, or open question — named specifically>
- ...

DONE
- <finished thread — one line, no retelling>
- ...
```

Name real decisions, files, and questions. "The important context" is not an inventory entry.

Then add the section for what was asked, and nothing else:

- **Trimming** → the command to run. Almost everything DONE → `Run: /clear`, one line saying why. Otherwise a focus string dense enough to survive compaction:
  `Run: /compact Keep: decided on JWT refresh tokens; auth/session.ts mid-refactor; open question on token TTL. Drop: the cookie-vs-JWT debate, the abandoned Redis prototype.`
- **Handoff / resumption** → the LIVE list plus one line on the immediate next step.
- **Write-up** → LIVE reordered as outcomes rather than state.

Don't restate the conversation, and don't explain the sort. The lists are the deliverable.

## Common mistakes
- Trying to execute `/compact` or `/clear` — a skill cannot; hand over the command instead.
- Cutting the only record of an unresolved decision. When unsure, KEEP.
- A vague focus string ("keep the important context") — compaction can only hold what it is told to hold.
- Running on a session that doesn't need it. "Nothing to cut here" is a valid, complete answer.
- Auditing on a size trigger. Transcript size measures *big*, not *cluttered* — a 30 MB session of dense work needs nothing, a 3 MB session of flailing needs this badly. Invoke at boundaries instead.
