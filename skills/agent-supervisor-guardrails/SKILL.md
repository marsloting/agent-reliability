---
name: agent-supervisor-guardrails
description: Use when supervising a long-running execution agent, recovering a stalled owner, checking handoff artifacts, or deciding whether to wait, remind, take over, or stop.
---

# Agent Supervisor Guardrails

Use this skill when one agent supervises another long-running execution owner.

The supervisor is not the worker. It keeps the work trustworthy by checking the owner, the artifacts, and the handoff ledger before deciding what to do next.

Core rule:

> File and artifact facts beat status claims.

## Quick Start

1. Name the current owner.
2. Re-read the task rules and acceptance condition.
3. Check whether artifacts are changing.
4. Check whether the owner left an inheritable ledger.
5. Decide: wait, remind, take over, or stop.
6. Stop on high-risk boundaries.

## When To Use

Use this skill when:

- an agent is running a long task and another agent is asked to watch it;
- a task can stall without a visible error;
- generated files, reports, drafts, or batches must be checked before acceptance;
- takeover is possible, but only after a concrete stall or missing handoff;
- multiple agents could accidentally write the same output batch.

Do not use this skill to approve publishing, deployment, secrets, payments, account changes, or legal/moderation actions.

## Patrol Checklist

On every patrol, collect facts before deciding.

### 1. Owner State

- owner id or name;
- current claimed state;
- last visible progress;
- whether the owner is active, stale, errored, done, or unreachable.

### 2. Artifact Facts

- expected outputs;
- actual output count;
- file paths or public URLs;
- modified time;
- size, dimensions, hash, or metadata when relevant;
- active versus superseded outputs.

### 3. Ledger State

Check whether another agent can inherit without guessing:

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

If this cannot be filled, the task is not safely inheritable.

## Decision Rules

### Wait

Wait when the owner is reachable and artifacts are still changing.

Do not interrupt slow but real progress.

### Remind

Send one short reminder when the owner is reachable and only a small ledger or status update is missing.

Reminder shape:

```text
Continue the current scoped task. First write the current ledger: completed artifacts, active paths, remaining gaps, and the exact next stop point. Do not publish, deploy, touch secrets, spend money, change accounts, or broaden scope.
```

If the owner cannot produce the ledger after one reminder, treat the handoff as defective.

### Take Over

Prepare takeover only after a concrete stall, unrecoverable error, or missing handoff.

Before takeover:

1. Re-read the task rules.
2. Reconstruct the ledger from artifacts and status notes.
3. Confirm no other owner is writing the same batch.
4. Mark active and superseded artifacts.
5. Name the exact remaining work.
6. Preserve forbidden actions and stop rules.

Do not take over because the owner is merely slow.

### Stop

Stop and ask the human before:

- public posting, publishing, promotion, or broad repository mutation;
- production deploy, rollback, or release;
- secrets, API keys, credentials, accounts, or identity changes;
- payments, merchant setup, KYC, tax, sponsorship, or invoices;
- legal, DMCA, moderation, complaint, or ban-risk surfaces;
- broadening beyond the current acceptance condition.

## Artifact Verification

Prefer direct checks over summaries.

Strong checks:

- reopen the final file or URL;
- list produced paths;
- inspect modified time;
- compare output count with expected count;
- verify dimensions, hash, or metadata;
- confirm the consumer-facing artifact exists where the next step expects it.

Weak checks:

- the owner says "done";
- the task appears in a list;
- a command returned 0;
- a screenshot shows a draft;
- an old note said it was nearly finished.

## Common Failure Modes

- **Silent stall**: the owner looks active, but artifacts stop changing.
- **False done**: the owner reports completion, but the consumer-facing artifact is missing.
- **Ledger gap**: outputs exist, but the next agent cannot tell which are active.
- **Duplicate ownership**: two agents write the same batch and overwrite each other.
- **Scope creep during recovery**: takeover turns repair into a new project.
- **High-risk overreach**: monitoring gets treated as permission to publish or mutate accounts.

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
