# support-kb-4471

Support ticket #4471 merged 412 reports and was marked known: one cause, one fix queued.
Checking the merged reports' own data shows three distinct causes, not one.

- [`support-kb/`](support-kb) — the support desk's knowledge base export, unmodified, plus one
  added page: [`src/routes/kb/incidents/4471-split/+page.svx`](support-kb/src/routes/kb/incidents/4471-split/+page.svx)
  and the component it uses, [`src/lib/kb/ThreeCauseTable.svelte`](support-kb/src/lib/kb/ThreeCauseTable.svelte).
- [`render/`](render) — a hand-built static render of the new page (`4471-split.html` +
  screenshots), since the export ships no build tooling to render it live. Every class, color
  token and markup shape in it is copied from the source files in this repo.

## The three problems

| cause | reports | ticket |
|---|---|---|
| `parser` — shared parser reads month-first | 374 | [#1](https://github.com/kcprnoj/support-kb-4471/issues/1) |
| `hint` — booking-v3's hint mismatches a day-first tenant | 33 | [#2](https://github.com/kcprnoj/support-kb-4471/issues/2) |
| `drift` — one-day difference, not a day/month swap | 5 | [#3](https://github.com/kcprnoj/support-kb-4471/issues/3) |

374 + 33 + 5 = 412.
