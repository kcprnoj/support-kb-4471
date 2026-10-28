# support-kb-4471

Support ticket #4471 merged 412 reports and was marked known: one cause, one fix queued. The
desk's own `CauseTable` already computes a second cause (`hint`) living in the same ticket, and
its own `oneDayMove` note already flags 5 reports that don't fit either cause. Three problems,
not one — using the desk's own counts, as exported, not a recomputed split.

- [`support-kb/`](support-kb) — the support desk's knowledge base export, unmodified, plus one
  added page: [`src/routes/kb/incidents/4471-split/+page.svx`](support-kb/src/routes/kb/incidents/4471-split/+page.svx),
  which reuses the desk's existing `CauseTable.svelte` rather than adding new bucketing logic.
- [`render/`](render) — a hand-built static render of the new page (`4471-split.html` +
  screenshots), since the export ships no build tooling to render it live. Every class, color
  token and markup shape in it is copied from the source files in this repo.

## The three problems

| cause | reports (the desk's own count) | ticket |
|---|---|---|
| `parser` — shared parser reads month-first | 379 (5 of these are the one-day move, below) | [#1](https://github.com/kcprnoj/support-kb-4471/issues/1) |
| `hint` — booking-v3's hint mismatches a day-first tenant | 33 | [#2](https://github.com/kcprnoj/support-kb-4471/issues/2) |
| one-day move — a one-day difference, not a day/month swap | 5 (named by the desk's own `oneDayMove` note; a subset of the 379 above) | [#3](https://github.com/kcprnoj/support-kb-4471/issues/3) |
