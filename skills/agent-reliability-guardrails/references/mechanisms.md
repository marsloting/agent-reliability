# Mechanisms

These mechanisms come from one public-cleared production scar:

> An app update left seven scheduled agent jobs visible, but their instruction bodies were empty. The metadata looked normal, the jobs did not run for days, and no error surfaced. Rebuilding exposed more state traps: create returned `already-exists`, update returned `ENOENT`, a closed scheduler window skipped runs without catch-up, and moved output directories caused silent loss until the jobs were recreated from source.

The public lesson is not "be careful with schedulers." The lesson is that agent systems need proof from the consumer end, because metadata can stay healthy while work dies.

## 1. Consumer-End Evidence

**Scar mapping**

- Symptom: seven jobs appeared to exist, but the instructions they needed to run were gone.
- Discovery: expected outputs stopped arriving; the task list still looked normal.
- Root cause: metadata was treated as proof of operation.
- Fix sequence: inspect the actual job bodies, rebuild them from a durable source, then add a health check based on fresh committed output.
- Evidence required next time: the consumer sees a fresh output from the job, not merely a job-list entry.

**Use this when**

An agent claims it wrote, sent, scheduled, published, shipped, or completed something that another system or person must consume.

**Rule**

> Verify where the failure would be visible, not where the agent did the work.

## 2. Durable Source of Truth

**Scar mapping**

- Symptom: the only live copy of scheduled task bodies lived inside the app state that the update damaged.
- Discovery: recovery required rebuilding seven jobs after the silent wipe.
- Root cause: task configuration had no durable source outside the runtime surface.
- Fix sequence: keep task bodies in files or specs that can recreate the runtime state, then compare runtime state against that source after changes.
- Evidence required next time: runtime configuration matches a durable source, and a fresh run proves the source was actually consumed.

**Use this when**

An agent relies on a UI, scheduler, dashboard, local app state, or third-party control plane as the only place where instructions live.

**Rule**

> If losing app state would erase the instructions, the automation is not backed up.

## 3. Contradictory-State Reconciliation

**Scar mapping**

- Symptom: recovery hit a ghost state. Create returned `already-exists`; update returned `ENOENT`.
- Discovery: normal repair commands disagreed about whether the job existed.
- Root cause: the control plane had separate metadata and body state, and they drifted.
- Fix sequence: stop looping the same command; enumerate actual state, rebuild from the durable source, and verify through a fresh consumer output.
- Evidence required next time: a readback of the reconstructed body plus a consumer-end run result.

**Use this when**

Tools return mutually inconsistent state, or when a successful update does not change the observable output.

**Rule**

> Contradictory state is not a retry problem. Reconcile from source and verify at the consumer.

## 4. Scheduler Health Check

**Scar mapping**

- Symptom: the scheduler window being closed caused missed runs without backfill.
- Discovery: the absence of expected output, not an alert, revealed the failure.
- Root cause: "schedule exists" was treated as "schedule ran."
- Fix sequence: add a daily health check that requires fresh output from every expected job.
- Evidence required next time: last successful run timestamp plus output receipt for each job.

**Use this when**

An agent monitors recurring tasks, scheduled jobs, cron-like flows, or any long-running automation.

**Rule**

> A schedule is healthy only when recent consumer output proves it ran.
