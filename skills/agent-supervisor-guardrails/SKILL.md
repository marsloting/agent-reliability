---
name: agent-supervisor-guardrails
description: Supervise a long-running execution agent without becoming the worker. Use when an agent must patrol another owner, verify artifacts and ledgers, recover from a stalled owner, or decide whether to wait, remind, take over, or stop. Requires artifact facts, inheritable handoff state, and high-risk stop boundaries before accepting or continuing work.
---

# Agent Supervisor Guardrails

Use this skill when one agent supervises another long-running execution owner.

The supervisor is not the worker. Its job is to keep the work trustworthy: check the owner, check the artifacts, check the ledger, then decide whether to wait, remind, take over, or stop.

## Operating Contract

A supervisor must not accept an owner's self-report as proof.

The core rule:

> File and artifact facts beat status claims.

If the owner says work is done, verify the produced artifacts from the path or surface the next consumer will use. If the owner says it is still running, verify whether anything is actually changing.

## When To Use

Use this skill when:

- an agent is running a long task and another agent is asked to watch it;
- a task can stall without throwing a visible error;
- generated artifacts, reports, drafts, or batches must be checked before acceptance;
- an owner needs a reminder, but may still be actively working;
- takeover is possible, but only after a concrete stall or unrecoverable handoff gap;
- multiple agents could accidentally write the same output batch.

Do not use it to authorize publishing, secrets, deployment, payments, account changes, or other high-risk actions.

## Patrol Checklist

On every patrol:

1. Re-read the current task rules and acceptance condition.
2. Identify the current owner and whether it is active, stale, errored, or done.
3. Check artifact facts:
   - expected files or outputs;
   - file count;
   - modified time;
   - size or dimensions when relevant;
   - hashes when useful;
   - whether outputs are active or superseded.
4. Check the owner ledger:
   - current scoped task;
   - stop point;
   - forbidden next steps;
   - produced artifacts;
   - remaining gaps.
5. Compare artifact facts with the claimed status.
6. Decide: wait, remind, take over, or stop.

## Decision Rules

| State | Decision |
|---|---|
| Owner is active and artifacts are still changing | Wait. Do not interrupt. |
| Owner is active but only missing a small ledger/status update | Send one short reminder. |
| Owner is stale, unreachable, errored, or leaves unusable artifacts | Prepare takeover. |
| The task requires publish, secrets, deploy, payment, account, legal, or identity action | Stop and ask the human. |
| Another owner may be writing the same output batch | Stop until ownership is clear. |
| The artifact facts contradict the done claim | Mark incomplete or failed. |

## Reminder Protocol

Use reminders sparingly. A reminder is for a reachable owner that likely can finish a small missing step.

Reminder shape:

```text
Continue the current scoped task. First write the current ledger: completed artifacts, active paths, remaining gaps, and the exact next stop point. Do not publish, deploy, touch secrets, spend money, change accounts, or broaden scope.
```

If the owner cannot produce the ledger after one reminder, treat the handoff as defective.

## Takeover Threshold

Takeover is allowed only after a concrete stall or unrecoverable missing handoff.

Before takeover:

1. Re-read the current task rules.
2. Reconstruct the ledger from local artifacts and status notes.
3. Confirm no other owner is writing the same batch.
4. Mark which artifacts are active and which are superseded.
5. Name the exact remaining work.
6. Preserve the stop rules and forbidden actions.

Do not take over just because the owner is slow. Do not take over from curiosity. Do not use takeover as permission to expand scope.

## Inheritable Ledger

Every supervised task should leave a ledger that another agent can inherit without guessing:

```text
scope:
current_owner:
status:
stop_point:
forbidden_actions:
active_artifacts:
superseded_artifacts:
artifact_facts:
remaining_gaps:
next_safe_action:
human_approval_needed:
```

If this ledger cannot be filled, the task is not safely inheritable.

## Artifact Verification

Prefer direct artifact checks over narrative summaries.

Useful checks:

- reopen the final file or URL;
- list produced paths;
- inspect modified time;
- compare file count to expected count;
- verify dimensions, hash, or metadata;
- confirm the consumer-facing artifact exists where the next step expects it.

Weak checks:

- owner says "done";
- task appears in a list;
- a command returned 0;
- a screenshot shows a draft;
- an old status note said it was nearly finished.

## High-Risk Stop Rules

Stop and ask the human before:

- public posting, publishing, or promotion;
- production deploy, rollback, release, or repository mutation outside the approved scope;
- secrets, API keys, credentials, account settings, or identity changes;
- payments, merchant setup, KYC, tax, sponsorship, or invoices;
- legal, DMCA, moderation, complaint, or ban-risk surfaces;
- broadening the task beyond the current acceptance condition.

## Common Failure Modes

- **Silent stall**: the owner looks active, but artifacts stop changing.
- **False done**: the owner reports completion, but the consumer-facing artifact is missing.
- **Ledger gap**: outputs exist, but the next agent cannot tell which are active.
- **Duplicate ownership**: two agents write the same batch and overwrite each other.
- **Scope creep during recovery**: takeover turns a repair task into a new project.
- **High-risk overreach**: a supervisor treats monitoring as permission to publish or mutate accounts.

## Output Shape

End with:

```text
Status: WAITING | REMINDED | TAKEOVER-READY | STOPPED | VERIFIED | INCOMPLETE
Owner state:
Artifact facts:
Ledger state:
Decision:
Next safe action:
Human approval needed:
```

If the status is not `VERIFIED`, give the smallest check or action that would move it forward.
