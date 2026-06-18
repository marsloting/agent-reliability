---
name: agent-reliability-guardrails
description: Verify agent completion claims before accepting them. Use when an agent says work is done, written, sent, shipped, monitored, scheduled, or fixed; when reviewing autonomous agent outputs; when a workflow crosses files, tools, schedulers, inboxes, repos, or other consumer boundaries; or when a user asks for proof that agent work actually happened. Produces consumer-end evidence receipts and rejects upstream proxies such as return codes, task-list presence, screenshots of drafts, or agent self-reports.
---

# Agent Reliability Guardrails

Use this skill to decide whether an agent's "done" claim is safe to accept.

The core rule:

> A completion claim is only proven by evidence from the place where the failure would show up.

If the claim affects a consumer, verify at the consumer end. If the claim is local and narrow, keep the proof narrow.

## Quick Workflow

1. Restate the claim in one sentence.
2. Name the consumer. Ask: "Who or what would notice if this were false?"
3. List the evidence offered by the agent.
4. Reject upstream proxies for consumer-facing claims.
5. Collect or request consumer-end evidence.
6. Write a receipt using `references/receipt-template.md`.
7. If the receipt cannot be filled, mark the claim unverified instead of softening it.

## Evidence Rules

Accept evidence only when it matches the claim.

- `File exists` proves only that a file exists.
- `Command exited 0` proves only that the command returned successfully.
- `Task appears in the scheduler` proves only metadata presence.
- `Agent says it wrote/sent/published` proves nothing by itself.
- `Consumer read it, parsed it, displayed it, ran it, or produced the expected output` can prove a consumer-facing claim.

When the claim is broad, move verification downstream.

| Claim | Weak evidence | Stronger evidence |
|---|---|---|
| "I wrote the report" | local write success | reopen the final file at the path the reader uses |
| "I scheduled the job" | job appears in the list | job has body, next-run metadata, and a fresh committed output |
| "I sent the handoff" | message write succeeded | receiver echo or downstream ingestion confirms it |
| "I fixed the bug" | patch applied | failing case now passes through the real acceptance path |
| "I published it" | push command exited 0 | public URL, fresh clone, or API readback shows the shipped artifact |

## When to Read References

- Read `references/mechanisms.md` when the claim involves scheduled jobs, handoffs, hidden state, app updates, retries, or "looks normal but nothing happened."
- Read `references/receipt-template.md` before writing the final verification receipt.
- Read `references/worked-example-scheduled-task-silent-death.md` when you need a concrete example of turning a scar into a proof receipt.

## Stop Rules

Stop and report "unverified" when:

- the only evidence is upstream of the consumer;
- the consumer cannot be named;
- the agent cannot reproduce the artifact from the consumer side;
- the check relies on a remembered contract that has not been re-read;
- a tool reports contradictory state, such as "already exists" on create and "not found" on update.

Do not invent proof to make a task feel complete. A clean "unverified" is safer than a confident false done.

## Output Shape

End with one of these statuses:

- `VERIFIED`: consumer-end evidence matches the claim.
- `PARTIAL`: narrow evidence is valid, but the broad claim is not proven.
- `UNVERIFIED`: available evidence does not prove the claim.
- `FAILED`: consumer-end evidence contradicts the claim.

For `PARTIAL`, `UNVERIFIED`, or `FAILED`, give the smallest next check that would move the status.
