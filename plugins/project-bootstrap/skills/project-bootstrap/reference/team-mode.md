# Team mode — the overlay for more than one contributor

The base system was shaped for a solo developer: a single writer, a single "current stage," commits
straight to the main branch. That model quietly breaks with two or more people. This overlay changes only
what has to change; the rest of the skill stands. Apply it when the KICKOFF interview answer is "team," or
when ADOPTing a repo that already has multiple contributors (multiple committers in `git log`, an existing
PR history).

## The core problem: living docs don't survive concurrent writing

`PROJECT_STATE.md`, `glossary.md`, `risks.md`, and `ROADMAP.md` are single files everyone appends to. On a
team that means a merge conflict every time two people touch the same section. The fixes:

- **PROJECT_STATE is written by one owner at a stage boundary, not by everyone continuously.** Solo mode
  encourages frequent edits; on a team that's the conflict engine. Instead: individual work is tracked in
  the issue tracker (below), and PROJECT_STATE is reconciled at the close of a stage by whoever owns it —
  as a summary, append-only at the end of each section. Never rewrite someone else's Done entry.
- **ADR numbers collide.** Two people both grab `0015-*.md` on parallel branches. Allocate the number when
  the PR opens (claim it in the PR description) rather than off the local file list, or key the ADR off the
  issue number instead of a running counter. A collision at merge is a rename, not a lost decision.
- **Prefer append-at-end** for glossary/risks rows too, so diffs don't overlap.

## Branch, PR, and review workflow

- **No direct commits to the main branch.** Every change is a branch → PR. (Solo mode commits to main; team
  mode never does.)
- **One unit of work = one branch = one PR**, small enough to review in a sitting. Branch pattern in
  `conventions.md` (`<type>/<slug>`).
- **CI green is the definition of "green"** — not "passed on my machine." Local checks are for fast
  feedback; the merge gate is the CI run on the PR.
- **Review is required.** At least one approving review; reviewers come from `CODEOWNERS` for the areas
  touched. All review threads resolved before merge.
- Scaffold a **`CODEOWNERS`** file (template) so reviews route automatically, including to the maintainers
  for changes to the project's self-description (PROJECT_STATE, ROADMAP, ADRs, conventions).

## Work tracking keys off issues, not a single banner

RESUME's "read the one current-stage banner" assumes one worker. With parallel workstreams:

- **Link work to the issue tracker** (GitHub Issues / Jira / Linear). The tracker is the backlog and the
  assignment mechanism; `PROJECT_STATE.md` Open questions reference issue IDs rather than *being* the
  to-do list for N people.
- On session start, orient off **the assigned issue / the current branch**, not a global "where did we all
  leave off." Read PROJECT_STATE for shared context, then the specific issue for the task.

## Definition of Done gains two gates

On top of the solo checklist (tests green, lint clean, PROJECT_STATE updated, decisions recorded):

- **CI is green on the PR.**
- **The PR is approved** by the required reviewer(s), with no unresolved comments.

## Onboarding and decisions

- Scaffold a **`CONTRIBUTING.md`** (template) — the human-facing setup + workflow guide, distinct from
  `CLAUDE.md` (which is for the agent). A new teammate should be productive from it alone.
- **Decisions flow through review:** open an ADR as `Status: Proposed` in the PR, let the team weigh in,
  and flip it to `Accepted` when the PR merges. This is how a decision made in chat or a meeting lands
  durably instead of evaporating.

## What NOT to change

Don't bureaucratize a two-person project as if it were fifty. Scale the ceremony: a small team may skip
CODEOWNERS and require just one review; a larger one needs the full set. The interview's team-size answer
sets the dial.
