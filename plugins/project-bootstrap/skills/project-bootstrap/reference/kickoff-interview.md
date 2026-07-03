# Running the kickoff interview

The kickoff interview seeds the whole document system, so it's worth doing well — but its real skill is
knowing what *not* to ask. Every question you ask that you could have answered yourself is friction, and
too much of it makes the discipline feel bureaucratic. The bar: ask only what changes what you scaffold
and can't be inferred.

## Infer before you ask

Before asking anything, look:
- List the directory. An existing `package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`, or
  `pubspec.yaml` tells you the stack — don't ask.
- Read any existing README or notes.
- Re-read the current conversation. The user has often already told you the goal and stack in passing.

## What to ask (only if unanswered)

Keep it to 4-6 questions, ideally batched into one `AskUserQuestion` call:

1. **Product / problem** — what is this and what does it do? (If unclear from context.)
2. **Components & stack** — one service or several? What language/framework each?
3. **Solo or team?** — drives how formal branches and commits should be. Solo → don't over-engineer
   branch conventions. Team → apply the Team mode overlay (`team-mode.md`): scaffold `CONTRIBUTING.md` +
   `CODEOWNERS`, branch/PR/review workflow instead of commit-to-main.
4. **Hard constraints** — budget, hardware ceilings, licensing (e.g. AGPL/GPL that affects distribution),
   deadlines.
5. **Horizon** — throwaway prototype, MVP, or long-lived product? This is the most important one because
   it sets *how much* to scaffold.

## Let the horizon set the depth

Don't apply the full apparatus to a weekend prototype — that's the fast way to make the user resent the
skill. Scale it:

| Horizon | Scaffold |
|---|---|
| Throwaway / spike | `CLAUDE.md` (light) + a `NOTES.md` or minimal `PROJECT_STATE.md`. Skip ROADMAP and ADRs. |
| MVP | `CLAUDE.md` + `PROJECT_STATE.md` + `conventions.md` + ADR-0001 + `ROADMAP.md`. |
| Long-lived product | Everything, plus `docs/research/` seeded and a fuller ROADMAP with named stages. |

## Turn answers into artifacts in one pass

Once you have the answers, generate the documents together so the project is coherent from the first
commit — the stack answer fills both `CLAUDE.md`'s commands table and `PROJECT_STATE.md`'s stack snapshot;
the components answer suggests commit scopes in `conventions.md`; the biggest structural choice
(monorepo vs. polyrepo, framework selection) becomes ADR-0001.

Then propose — don't perform — the first commit. The git rules say commit only when asked.
