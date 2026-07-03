# When a decision earns an ADR

Not every choice deserves a document. Over-documenting trivial picks buries the important ones and makes
the practice feel like paperwork. The judgment call is about *durability and cost*, not size.

## Write an ADR when the decision is any of:

- **Expensive to reverse.** Undoing it later means data migration, a broad refactor, or breaking clients.
  (Repo structure, database engine, primary schema shape, auth model, public API contract.)
- **Cross-component.** It constrains more than one service/module, so people working elsewhere need to
  know the reasoning, not just the outcome.
- **A real pick between viable alternatives.** There were 2-3 defensible options and you chose one for
  reasons that won't be obvious from the code. The ADR preserves *why not the others*.

If it clears any of these bars, it's cheap insurance: `templates/adr-NNNN.md.tmpl`, next number in
`docs/decisions/`, plus a one-line link under Decisions in `PROJECT_STATE.md`.

## A conventions-line (not an ADR) when:

- It's a small, cross-cutting rule with an obvious rationale — file naming, commit format, import
  ordering, a lint setting. Append to `docs/conventions.md`.

## Nothing at all when:

- It's a local, easily-reversed implementation detail — a variable name, a helper's internal structure,
  which of two equivalent stdlib calls to use. Recording these is noise.

## Litmus test

Ask: *"If someone revisits this in six months and asks 'why is it this way,' will the code alone answer
them?"* If yes → no ADR. If the answer lives in tradeoffs the code can't show → ADR.

## Status hygiene

ADRs are immutable records, not living docs. When a later decision overrides an earlier one, don't edit
the old ADR's decision — set its **Status** to `Superseded by ADR-XXXX` and write the new one. The trail
of superseded decisions is itself valuable history.
