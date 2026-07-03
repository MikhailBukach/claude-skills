---
name: project-bootstrap
description: >
  Use this skill for starting or structuring software projects with lasting documentation. Trigger it
  when the user wants to: start, scaffold, set up, or kick off a new project, repo, service, or monorepo —
  especially "properly," "the right way," or "documented from day one"; establish or standardize repo
  conventions like commit-message formats, file-naming, or branching; see a written step-by-step plan
  before you edit any files; record an architectural or design decision (ADR); capture a hard-won
  debugging or research finding they don't want to lose; bring an existing undocumented codebase under
  documentation discipline (reconstruct state/conventions from its git history and code); or resume a
  project that tracks state in PROJECT_STATE.md, ROADMAP.md, or docs/decisions. The shared intent: durable project scaffolding,
  recorded decisions, agreed conventions, resumable state, or plan-before-coding — not diving straight
  into code. Skip it for one-off edits, a single file or lone component, README writing, product/feature
  roadmaps, or routine debugging with no intent to document.
---

# Project Bootstrap

A discipline for starting and running software projects so that **decisions are recorded, state is
resumable, and work never begins without a plan**. It replaces the per-session "here are my conventions"
prompt with durable, in-repo documents that Claude reads automatically.

The system rests on a few living documents and three routines. The documents are the memory; the routines
are how that memory gets read and written. Match the project's existing language (RU/EN/mixed) in
everything you generate — don't impose English on a Russian-documented repo.

## The documents

| File | Purpose | Cadence |
|---|---|---|
| `CLAUDE.md` | The anchor. Points every session at the other docs + records git/build/test rules. | Written once at kickoff, edited rarely. |
| `PROJECT_STATE.md` | Running log: current stage, what's Done, what's In progress, Decisions, Open questions, Stack. | Updated at the **end of every stage**. |
| `ROADMAP.md` | The plan — stages/milestones ahead. Separate from state so "the plan" and "where we are" don't tangle. | Edited when the plan shifts. |
| `docs/conventions.md` | Cross-cutting rules too small for an ADR (commits, file naming, branches). | Append as conventions emerge. |
| `docs/decisions/NNNN-*.md` | ADRs — one per hard-to-reverse or contested decision. | One file per qualifying decision. |
| `docs/research/*.md` | Dated investigation notes (hypothesis → what was tried → conclusion). | One per nontrivial investigation. |
| `docs/glossary.md` | Domain terms → one-line definitions, so meaning doesn't drift. | Add a row when a stage introduces a term. |
| `docs/risks.md` *(or a PROJECT_STATE section)* | Assumptions & risks — the load-bearing bets the plan stands on. | Update when a bet is added, validated, or broken. |
| `CONTRIBUTING.md` *(team)* | Human-facing onboarding: setup, branch/PR/review workflow, DoD. | Written at kickoff for a team; edited as process changes. |
| `CODEOWNERS` *(team)* | Who must review changes to each area. | Written at kickoff for a team; edited as ownership shifts. |

Templates for each live in `templates/` next to this file. Read the relevant template before generating a
document — they encode the exact structure that makes these files useful, not decorative.

## Routine 1 — KICKOFF (new project)

Triggered when the user is starting fresh. The goal is to stand up the document system in one pass so the
project is documented from commit zero — but **do not interrogate the user to death**. Ask only what you
can't infer from the conversation or a quick look at the directory.

1. **Interview** (short — 4-6 questions, skip any you can already answer):
   - What is the product / what problem does it solve?
   - What components or services will it have, and in what stack each?
   - Solo or team? (drives branch/commit formality)
   - Hard constraints — budget, hardware, licensing, deadlines?
   - Horizon — throwaway prototype, MVP, or long-lived product?

   Use `AskUserQuestion` when a choice genuinely changes what you scaffold; otherwise pick the sensible
   default and say so. Read `reference/kickoff-interview.md` for how to run this well and when to skip it.

2. **Scaffold**, adapting depth to the horizon (a throwaway prototype doesn't need a ROADMAP or ADRs):
   - `CLAUDE.md` from `templates/CLAUDE.md.tmpl` — this is the highest-value artifact; it makes future
     sessions auto-load context. Fill in the real build/test/lint commands and git rules.
   - `PROJECT_STATE.md` from `templates/PROJECT_STATE.md.tmpl` with the current-stage banner set.
   - `ROADMAP.md` from `templates/ROADMAP.md.tmpl` (skip for throwaway prototypes).
   - `docs/conventions.md` from `templates/conventions.md.tmpl`.
   - `docs/decisions/0001-*.md` — the first real decision (usually repo structure / stack choice),
     using `templates/adr-NNNN.md.tmpl`.
   - `docs/` and `docs/research/` directories.
   - `docs/glossary.md` from `templates/glossary.md.tmpl` — only if the project has real domain jargon
     (most do); skip for a plain CRUD app with no coined terms.
   - `.env.example` committed, `.env` in `.gitignore`, secrets as `rotate-me` placeholders.
   - **If the answer was "team":** also apply the Team mode overlay — scaffold `CONTRIBUTING.md`
     (`templates/CONTRIBUTING.md.tmpl`) and `CODEOWNERS` (`templates/CODEOWNERS.tmpl`), and use the
     branch/PR conventions instead of commit-to-main. See the Team mode section below.

3. **Offer** `git init` + a first `chore: scaffold project skeleton` commit — only with confirmation, per
   the git rules in `CLAUDE.md`. Never commit unasked.

## Routine 1b — ADOPT (existing project without the docs)

Triggered when the user wants to bring an *existing* codebase under this discipline. The difference from
KICKOFF is the source of truth: don't interview the user for what the repo already shows — **reconstruct
the docs from the code, the git history, and the existing commit style.** Asking what you could have read
is the fastest way to make this feel like busywork.

1. **Read the repo first.** Manifests (`package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`,
   `pubspec.yaml`) → stack. `git log` → what's been built, the real commit conventions in use, active
   areas. Directory layout → components. `README` / any scattered docs → intent. `TODO`/`FIXME` and open
   PRs → open questions.

2. **Reconstruct, don't invent.** Generate the documents *from evidence*, and where you're inferring
   rather than certain, say so instead of fabricating confidence:
   - `CLAUDE.md` — real build/test/lint commands (read the Makefile / scripts / CI), the git conventions
     already visible in the log, the actual stack.
   - `PROJECT_STATE.md` — **Done** reconstructed from git history (major milestones, not every commit);
     **In progress** from uncommitted work + recent unmerged branches; **Open questions** from TODO/FIXME
     and known-rough areas; **Stack snapshot** from manifests.
   - `docs/conventions.md` — the conventions the repo *already follows* (commit style inferred from the
     log, file naming from what's on disk), not an idealized set the team doesn't use.
   - `docs/decisions/0001-*.md` — one retroactive ADR for the biggest structural fork already taken
     (monorepo vs. polyrepo, framework, storage), written in past tense as a record.
   - `docs/glossary.md` and the risks register — only if the codebase has domain jargon / load-bearing
     bets worth capturing.

3. **Fill only the gaps.** If some of these already exist (a mature repo may have a ROADMAP and ADRs but
   no CLAUDE.md), don't overwrite them — add what's missing and reconcile, noting anything the existing
   docs contradict rather than silently "fixing" it.

4. **Show the plan before writing files**, and never commit unasked. A retrofit touches the repo's
   self-description, so the user should see what you'll create before you create it.

## Routine 2 — PLAN-BEFORE-EXECUTE (before nontrivial work)

The rule: **no nontrivial code before an approved, written plan.** This is the single highest-leverage
habit — it catches wrong turns while they're cheap and gives the user a decision point.

1. Draft a **numbered plan**: the concrete steps, files you'll touch, and how you'll verify. Use the
   harness's plan mode when available (`EnterPlanMode` / `ExitPlanMode`).
2. As you plan, flag any choice that's **expensive to reverse, cross-component, or a pick between viable
   alternatives** — those are ADR candidates (see `reference/decision-triggers.md`). Note them in the plan
   rather than silently deciding.
3. **Surface the plan's load-bearing assumptions.** If the plan quietly bets on something not obviously
   true — a memory/perf ceiling, an external dependency you don't control, a compatibility guess — name it,
   and for a bet that would be costly if wrong, log it in the assumptions & risks register
   (`templates/risks-register.md.tmpl`, or `docs/risks.md` / a PROJECT_STATE section). The point is that
   when a bet breaks later, you can see what was riding on it. This is distinct from an Open question: an
   assumption is something you're *building on as if true*, not a gap you know is open.
4. Get approval, then execute. If the plan changes materially mid-flight, surface it — don't quietly
   diverge from what the user approved.

"Nontrivial" is judgment, not a rule to lawyer. A one-line fix needs no plan; a new module, a schema
change, or anything touching multiple files does.

## Routine 3 — RESUME (session start) & DONE (stage close)

These bookend a working session and keep `PROJECT_STATE.md` honest.

**On session start**, when a `PROJECT_STATE.md` exists:
- Read the current-stage banner + Open questions.
- Skim `git log` since the last "Last updated" to see what actually landed.
- Propose the next step in one or two sentences. This replaces the manual "paste the context" ritual —
  the state file *is* the context.

**On stage close** — the "Done" gate. A stage isn't done until:
- Tests are green and the linter is clean (state the actual results — "N tests pass, analyze 0 issues",
  not "should be fine").
- `PROJECT_STATE.md` is updated: the finished work moved to Done, new Open questions recorded, the
  current-stage banner and `Last updated` line advanced.
- Any decision made during the stage is captured — an ADR if it qualifies, otherwise a line in
  `conventions.md`.
- Any new domain term the stage introduced is added to `docs/glossary.md`, and any assumption that got
  validated or broken is updated in the risks register.

Read `reference/definition-of-done.md` for the full checklist. The point of the gate is that the next
session (yours or a teammate's) can trust the state file completely.

## Recording decisions and research as you go

- **Decision worth remembering?** If it clears the bar in `reference/decision-triggers.md`, write an ADR
  from `templates/adr-NNNN.md.tmpl` (next number in `docs/decisions/`), and add a one-line summary with a
  link under Decisions in `PROJECT_STATE.md`. Small stuff → a line in `conventions.md` instead.
- **Investigated something nonobvious?** (a bug root cause, a perf finding, a spike comparing options) —
  drop a dated note in `docs/research/` from `templates/research-note.md.tmpl` so the reasoning survives.
  This is what keeps hard-won findings from evaporating between sessions.

## Team mode (overlay)

The base system assumes a solo developer — one writer, one "current stage," commits to main. With two or
more contributors that model breaks, so apply this overlay when the project is a team (KICKOFF answer, or
an ADOPTed repo with multiple committers). It changes only what must change:

- **Living docs survive concurrency.** `PROJECT_STATE.md` is written by one owner at stage boundaries, not
  by everyone continuously (that's the merge-conflict engine); append at the end of sections, never rewrite
  a teammate's entry. Claim ADR numbers when the PR opens so two branches don't both grab `0015`.
- **Branch → PR → review, never commit to main.** CI-green (not "passed on my machine") is the merge gate;
  reviews route via `CODEOWNERS`. The git rules in `CLAUDE.md` shift from "commit to main when asked" to
  "open a PR."
- **Work keys off the issue tracker,** not one global banner: RESUME orients off the assigned issue /
  current branch; PROJECT_STATE Open questions reference issue IDs rather than being the shared backlog.
- **DoD gains two gates** — CI green on the PR, and an approving review with no unresolved comments.
- **Onboard humans with `CONTRIBUTING.md`** (distinct from `CLAUDE.md`, which is for the agent); decisions
  flow through review as ADRs `Proposed` → `Accepted`.

Scale the ceremony to team size — don't bureaucratize a two-person project. Read `reference/team-mode.md`
for the full overlay.

## Why this exists

Every one of these routines exists to make work **resumable and trustworthy**. A project accrues context
faster than any single session holds it; without a written spine, each session re-derives what the last
one knew, decisions get silently reversed, and "why did we do it this way" becomes unanswerable. The
documents are cheap to maintain and compound in value. Keep them lean — a stale doc is worse than none,
so prune and update rather than hoard.

## Reference files

- `reference/kickoff-interview.md` — running the kickoff interview; what to ask vs. infer vs. skip.
- `reference/decision-triggers.md` — when a decision earns an ADR vs. a conventions line.
- `reference/definition-of-done.md` — the stage-close checklist in full.
- `reference/session-protocol.md` — the exact read-on-start / write-on-close steps.
- `reference/team-mode.md` — the multi-contributor overlay (branch/PR/review, concurrent docs, CI gates).
