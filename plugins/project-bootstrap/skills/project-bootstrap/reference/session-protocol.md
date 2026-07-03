# Session protocol — read on start, write on close

This is the loop that makes a project resumable. The state file is only worth maintaining if it's
actually *read* at the start of each session and *written* at the end — otherwise it drifts into fiction.

## On session start (resume)

When a `PROJECT_STATE.md` exists, before diving into whatever the user asked:

1. **Read the current-stage banner and Open questions.** This is 30 seconds and tells you where things
   stand and what's known-broken. Don't skip it and rediscover a documented gotcha the hard way.
2. **Diff against reality.** Run `git log --oneline` since the "Last updated" date (or the last several
   commits). The state file is updated at stage boundaries, so recent commits may be ahead of it — this
   catches work-in-flight the doc doesn't yet mention.
3. **Orient, briefly.** Offer the next step in a sentence or two grounded in what you just read: "State
   says stage 4 is in progress with the parser done; the open question about Unicode handling is next —
   want me to take that?" This replaces the user having to paste context every time.

Do this proactively when the user's request is vague or clearly a continuation. If they open with a
specific, self-contained task, honor that first — but still glance at Open questions in case their task
brushes a known landmine.

## On session close (stage boundary)

Trigger the Definition of Done gate (`definition-of-done.md`) when a coherent chunk of work finishes —
not after every commit, but at the natural "that's a thing now" moments. Concretely:

- Move finished work to Done with a factual entry.
- Record new Open questions — including anything you noticed but chose not to fix.
- Advance the current-stage banner and bump Last updated.
- Capture decisions (ADR or conventions line) and research notes.

## The failure mode this prevents

Without the read step, you operate from stale assumptions and re-derive what the last session knew.
Without the write step, the next session inherits nothing. Either half alone is nearly worthless — the
value is in the loop. When a session is short or interrupted, at minimum bump Open questions with whatever
you learned, even if the full gate is premature; a one-line "found that X breaks when Y" is a gift to the
next session.
