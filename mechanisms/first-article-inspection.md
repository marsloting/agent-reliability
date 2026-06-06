# First-article inspection

**Agent errors aren't random. They're systematic — one misreading, stamped across the whole batch. Spot-checks are built for human errors; they miss this completely.**

## Where it breaks

You hand an agent a batch job: 20 JSON files, 10 reports, 50 records. Instinct says spot-check — pull three, they look fine, ship the batch.

That instinct comes from managing humans. Human errors are random: fatigue, distraction, a typo here, a skipped line there. Random errors are what sampling statistics were built for — check 10%, estimate the defect rate, reasonable.

Agent errors don't work like that. Same prompt, same model, same misreading of the spec → **the same error, replicated across every item**. An agent that misreads one schema field doesn't get it wrong once — it gets it wrong twenty times, identically. The batch isn't 20 independent attempts. It's one attempt, copied 20 times.

Spot-checking a homogeneous batch tells you almost nothing: if the misreading happens to sit in the part you sampled, you catch it. If not, you just certified a batch that's 100% defective in a place you didn't look.

## What I hit in production

A batch of structured data files. The agent misread one field's contract — every file carried the same defect. The spot-check passed (wrong place to look). Downstream consumed the batch and failed at scale: rework cost = batch size × per-item fix, plus the downstream cleanup.

Manufacturing solved this decades ago. When a production line changes anything — new die, new material, new operator — the line doesn't start with a batch. It makes **one piece** and inspects it against the full spec. First-article inspection. Only when piece #1 passes completely does the batch run.

An agent given a new prompt is a production line after a die change. Every time.

## The defense

**Before any batch: produce one item and validate it against the full contract — every field, every requirement, the real acceptance check. Not a glance.**

- First item passes fully → release the batch. One full inspection buys certainty for the whole run.
- First item fails → **fix the instruction, not the item**. The error you found is in every item the batch would produce; hand-patching item #1 and then running the batch ships the same defect 19 more times.
- New prompt, new schema, new context = **new batch = new first article**. Manufacturing re-inspects after every changeover. So should you.

The corollary: sampling 2+ items of a homogeneous batch is mostly wasted inspection — items #2 through #20 carry the same information as item #1. Inspect one deeply instead of three shallowly.

## The rule — paste into `CLAUDE.md` / `AGENTS.md`

> Before producing any batch (>3 similar items), the first item must pass the FULL acceptance check against the contract — not a glance. If it fails, fix the instruction, not the item. A new prompt, schema, or context starts a new batch and a new first article.
