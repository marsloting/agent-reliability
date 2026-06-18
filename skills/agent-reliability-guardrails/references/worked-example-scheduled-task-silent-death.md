# Worked Example: Scheduled Task Silent Death

## Situation

An app update left seven scheduled agent jobs visible in the scheduler, but their instruction bodies were empty. The jobs looked alive from the task list. They produced nothing for days. No error appeared.

During recovery, normal repair paths contradicted each other:

- creating the job returned `already-exists`;
- updating the job returned `ENOENT`;
- a closed scheduler window skipped runs without backfill;
- moved output directories caused silent loss until jobs were recreated from source.

## Bad Verification

```markdown
Status: VERIFIED
Claim: The scheduled jobs are set up.
Evidence accepted: The scheduler lists seven jobs.
Result: Looks good.
```

This is corrupt success. The evidence proves metadata presence, not operation.

## Better Receipt

```markdown
## Agent Done-Claim Receipt

Status: VERIFIED

Claim:
- Seven scheduled jobs were restored and are running again.

Consumer:
- The downstream workflow that expects fresh output from each job.

Evidence accepted:
- Each job body was read back after reconstruction.
- The runtime body matched the durable source used to recreate it.
- The next scheduled run metadata existed.
- A fresh output from each job appeared in the downstream location that actually consumes it.
- The health check reported all seven jobs as recently run and committed.

Evidence rejected:
- The scheduler list showing seven jobs.
- Create/update return codes during ghost-state recovery.
- Agent self-report that the jobs were recreated.

Checks run:
- Enumerated all expected jobs.
- Compared live job body against durable source.
- Forced or waited for one run.
- Read the output from the downstream consumer location.

Result:
- The consumer received fresh output from all seven jobs.

Remaining risk:
- Future app updates could damage runtime state again.

Next check if not VERIFIED:
- If any job lacks fresh consumer output, mark that job FAILED and rebuild from source.
```

## Rule Extracted

> Scheduled automation is not healthy because the schedule exists. It is healthy only when the expected consumer has fresh output from the scheduled work.
