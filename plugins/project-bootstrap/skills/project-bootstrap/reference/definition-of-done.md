# Definition of Done — the stage-close gate

A stage is a coherent, shippable increment. The gate exists so that when a stage is marked "Done," the
next session — yours, or a teammate's, months later — can trust that claim completely and build on it
without re-verifying. A stage that's "basically done" but leaves the state file lying is worse than one
still openly in progress.

## The checklist

Before moving work from "In progress" to "Done" in `PROJECT_STATE.md`:

1. **Tests are green.** Run them. Report the actual result — "142 tests pass" — not "tests should pass."
   If some are skipped or failing, say which and why; a known-failing test is acceptable only if it's
   recorded in Open questions.

2. **Linter/formatter is clean.** Run it. "0 issues" is a fact you state, not an assumption. If the
   project has a type checker (mypy, tsc, `flutter analyze`, `cargo clippy`), it counts here too.

3. **PROJECT_STATE.md is updated:**
   - Finished work moved from "In progress" to "Done" with a specific, factual entry (what shipped, what
     was verified, notable commit hashes).
   - New **Open questions** / tech debt discovered during the stage are written down. This is the most
     commonly skipped step and the most valuable — the things you noticed but didn't fix are exactly what
     the next session needs to know.
   - The **current-stage banner** advanced to the next focus.
   - **Last updated** date bumped.

4. **Decisions captured.** Any choice made during the stage that clears the ADR bar
   (`decision-triggers.md`) is written as an ADR and linked; smaller rules appended to `conventions.md`.

5. **Research logged.** Any nonobvious finding (a bug's root cause, a spike result, a perf measurement)
   has a dated note in `docs/research/`.

6. **Glossary & risks current.** Any new domain term the stage coined is in `docs/glossary.md`; any
   assumption that got validated or broken during the stage is updated (not silently left stale) in the
   assumptions & risks register.

## Why "state the actual result" matters

The temptation is to write "should be fine" or "tests pass" from memory. But the whole point of the gate
is that the state file is *trustworthy* — and trust erodes the first time someone finds a "Done" stage
with red tests. Run the checks, read the output, report what it says. If the harness's own guidance is to
report outcomes faithfully, this is where it bites: an honest "3 tests still failing, tracked in Open
questions" is worth more than a confident lie.

## Commit, if asked

The gate is about documentation integrity, not git. Commit only when the user asks — but a clean stage
close is the natural moment to offer one.
