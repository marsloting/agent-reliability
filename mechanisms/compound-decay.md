# Compound decay

**A chain of "pretty reliable" steps is an unreliable system. 85% per step looks fine; eight steps of it is 27%. Nobody designs a coin flip on purpose — they design it by chaining.**

## Where it breaks

Per-step reliability compounds multiplicatively, and multiplication is merciless:

| Per-step | 5 steps | 8 steps | 20 steps |
|---:|---:|---:|---:|
| 95% | 77% | 66% | 36% |
| 90% | 59% | 43% | 12% |
| 85% | 44% | **27%** | 4% |

The trap isn't the math — everyone nods at the math. The trap is that **nothing in the workflow tells you the chain length**. You add a step here (fetch data), a step there (reformat, then write back, then notify), each one individually sensible, each one "the agent handles fine in testing." No single addition feels like a decision about system reliability. Then the end-to-end run fails most nights and every individual step still looks innocent, because each step *is* innocent. The failure lives in the multiplication, and the multiplication isn't written down anywhere.

This is arithmetic, not a model-quality problem. A better model moves 85% to maybe 92% — it does not repeal compounding ([the 80% reliability horizon](https://blog.apiad.net/p/the-80-ai-reliability-horizon), [multi-agent reliability math](https://www.mindstudio.ai/blog/multi-agent-reliability-compounding-problem-77-percent)).

## What I hit in production

An unattended overnight pipeline: read state, collect external data, cross-check, produce a report, write back, update logs. Eight-plus dependent steps. Each step had been tested alone and behaved. End-to-end, the run kept dying somewhere in the middle — a different step each night, which is exactly what compounding looks like from the inside: no step is the culprit, so no fix sticks.

What worked was not making any step smarter. It was **cutting the chain**: group steps into stages, end every stage with a machine-verifiable check, and persist the stage result. A verified checkpoint resets the multiplication — the chain stops being one 8-step gamble and becomes three short bets, each recoverable on its own.

## The defense

1. **Count the chain before you run it.** Write the number down. Eight dependent steps at 95% is a 66% system — decide if that's acceptable *before* the 3am failure, not after.
2. **Insert verified checkpoints every 3–4 steps.** A checkpoint is a machine-checkable acceptance (file exists with expected content, count matches, grep hits) — not the agent saying "done". Verification resets reliability; a claim doesn't.
3. **Checkpoint = rollback unit.** When stage 3 fails, you re-run stage 3 against stage 2's persisted output. Without checkpoints, every failure is a full restart — you pay the compound odds again on the retry.
4. **Deleting a step beats improving a step.** Dropping one step from an 8-chain at 85% buys more end-to-end reliability than any prompt engineering on the steps that remain. Shortest path to a reliable chain: a shorter chain.

## The rule — paste into `CLAUDE.md` / `AGENTS.md`

> Before running any chain of >5 dependent steps: state the step count, insert a machine-verifiable checkpoint at least every 3–4 steps, and persist each stage's output so a failed stage re-runs alone. If a chain can't be checkpointed, shorten it until it can.
