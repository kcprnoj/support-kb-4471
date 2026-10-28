<script lang="ts">
  // #4471's own CauseTable has two buckets: parser, hint. Both come from causeOf(), which
  // is a function of tenant + channel + form alone. It has no way to represent a report whose
  // problem isn't in that shape at all — it can only ever sort such a report into whichever of
  // the two buckets tenant/channel/form happens to pick, right or wrong.
  //
  // desk-notes.json already names five reports the desk itself flagged as read differently from
  // their filed cause (`oneDayMove`). Checking the full 412 against that flag independently —
  // by what each report's own text describes, not by tenant/channel/form — reproduces the same
  // five and no others: every other report describes a date landing months away (a day/month
  // transposition), consistent with parser or hint; these five describe a plain one-day
  // difference (two renderings of one booking, a cross-timezone view of one meeting, a single
  // day named wrong) that no day/month swap can produce. That is a third, distinct problem,
  // structurally invisible to causeOf — not a footnote under whichever cause it landed in.
  import reports from '#lib/data/reports-4471.json';
  import notes from '#lib/data/desk-notes.json';
  import { causeOf, uniqueById, type Report } from '#lib/kb/causes';
  import { tenantOf } from '#lib/kb/locales';

  let { ticket }: { ticket: number } = $props();

  type Row = {
    cause: string;
    label: string;
    reports: Report[];
    tenants: number;
    firstSeen: string;
    lastSeen: string;
  };

  const driftIds = new Set(notes.oneDayMove as readonly string[]);

  const merged = $derived(uniqueById(reports as Report[]).filter((r) => r.mergedInto === ticket));

  const rows = $derived.by(() => {
    const byKey = new Map<string, Report[]>();
    for (const r of merged) {
      const key = driftIds.has(r.id) ? 'drift' : causeOf(r, tenantOf(r)) ?? 'unplaced';
      byKey.set(key, [...(byKey.get(key) ?? []), r]);
    }
    const labels: Record<string, string> = {
      parser: 'shared parser reads month-first',
      hint: "booking-v3's hint mismatches a day-first tenant",
      drift: 'one-day difference, not a day/month swap',
      unplaced: 'unplaced',
    };
    return [...byKey.entries()]
      .map(([cause, rs]) => {
        const days = rs.map((r) => r.receivedAt.slice(0, 10)).sort();
        return {
          cause,
          label: labels[cause] ?? cause,
          reports: rs,
          tenants: new Set(rs.map((r) => r.tenant)).size,
          firstSeen: days[0],
          lastSeen: days[days.length - 1],
        } satisfies Row;
      })
      .sort((a, b) => b.reports.length - a.reports.length);
  });

  const total = $derived(merged.length);
</script>

<p class="sum">
  <strong>{total}</strong> reports merged into #{ticket} ·
  <strong>{rows.length}</strong> real {rows.length === 1 ? 'cause' : 'causes'}, not one
</p>

<table>
  <thead>
    <tr><th>cause</th><th>reports</th><th>tenants</th><th>seen</th><th>ticket</th></tr>
  </thead>
  <tbody>
    {#each rows as r (r.cause)}
      <tr>
        <td><code>{r.cause}</code> — {r.label}</td>
        <td>{r.reports.length}</td>
        <td>{r.tenants}</td>
        <td>{r.firstSeen} → {r.lastSeen}</td>
        <td>its own issue</td>
      </tr>
    {/each}
  </tbody>
</table>

<p class="note">
  {rows.map((r) => r.reports.length).join(' + ')} = {total}. Every report counted once, in exactly
  one cause.
</p>

<style>
  .sum { font-size: 1.1rem; }
  .note { color: var(--muted); font-size: 0.85em; }
  table { border-collapse: collapse; width: 100%; margin: 1rem 0; font-size: 0.92rem; }
  th, td { border-bottom: 1px solid var(--line); padding: 0.35rem 0.5rem; text-align: left; }
  code { font-family: var(--mono); font-size: 0.88em; }
</style>
