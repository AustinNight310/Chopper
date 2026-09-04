---
name: Chopper
description: Use when you need to know what is still live in the current session — resuming work after a break, handing off to another session or teammate, writing up what happened, deciding whether to keep going or start fresh, or aiming a /compact before running it. Trigger on "chopper", "what's live", "what's still relevant", "session inventory", "audit context", "what can we clear", "trim the context", "where were we".
---

# Chopper

## Overview
Takes inventory of the current session: reads the conversation back, pulls the durable conclusion out of each thread, and cuts what's left. One audit, several uses — handoff, resumption, write-up, and aiming a context trim all need the same answer.

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
This is **not** sorting threads into two bins. A finished thread usually leaves a
conclusion behind that is still binding, and the two travel separately:

- A failed experiment is dead; *why it failed* governs the next attempt.
- A superseded design is dead; the file it produced is still on disk.
- A resolved debugging chase is dead; the constraint it uncovered still holds.

Sorting whole threads forces one verdict on both halves, so you either keep fifteen
turns to protect one sentence, or cut the sentence along with the turns.

Walk the conversation top to bottom. For each thread, in this order:

1. **Extract.** Does this leave anything that governs future work — a decision, a
   constraint, a file, an unanswered question, a fact about how this environment
   behaves? Write it into LIVE as one standalone line. It must stand on its own with
   the thread deleted; if it only makes sense in context, it isn't extracted yet.
2. **Cut the body.** Everything else in that thread goes to DONE: the attempts, the
   tool output, the reasoning, the retelling.

A thread can yield nothing (pure tool output) — that's fine, it goes straight to DONE.
A thread can be entirely live (an open question) — then it has no body to cut.

When genuinely unsure: **keep the conclusion, cut the body.** Preserving a whole thread
because one line inside it might matter is the failure this skill exists to prevent —
if the line matters, extract it; if you cannot state it in one line, you have not
understood it well enough to keep it.

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

Then add exactly one closing section, and nothing else. **Invoked with no argument, the
answer is trimming** — that is the default, not a guess:

- **Trimming** (default) → the command to run. Almost everything DONE → `Run: /clear`,
  one line saying why. Otherwise a `/compact` focus string, **6 clauses maximum**:
  `Run: /compact Keep: JWT refresh tokens decided; auth/session.ts mid-refactor; token TTL open. Drop: the cookie-vs-JWT debate; the abandoned Redis prototype.`
- **Handoff / resumption** → the LIVE list plus one line on the immediate next step.
- **Write-up** → LIVE reordered as outcomes rather than state.

**The focus string is a pointer, not the record.** A real session produces far more LIVE
lines than fit in one; six is what a person will actually run and what compaction can
act on. Pick the six that would be most expensive to re-derive, and let the LIVE list
above stand as the full record — it is already on screen, and the user can paste from it.

Don't restate the conversation, and don't explain the sort. The lists are the deliverable.

## Common mistakes
- Trying to execute `/compact` or `/clear` — a skill cannot; hand over the command instead.
- **Keeping a whole thread to protect one line inside it.** Extract the line; cut the thread.
- Cutting the only record of an unresolved decision. When unsure, keep the conclusion.
- Leaving a LIVE entry that only makes sense in context — it will not survive the cut that follows.
- A vague focus string ("keep the important context") — compaction can only hold what it is told to hold.
- A focus string that tries to carry the whole LIVE list. Six clauses; the list is the record.
- Running on a session that doesn't need it. "Nothing to cut here" is a valid, complete answer.
- Auditing on a size trigger. Transcript size measures *big*, not *cluttered* — a 30 MB session of dense work needs nothing, a 3 MB session of flailing needs this badly. Invoke at boundaries instead.
