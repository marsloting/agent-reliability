# Receipt Template

Use this template whenever you verify an agent done-claim.

```markdown
## Agent Done-Claim Receipt

Status: VERIFIED | PARTIAL | UNVERIFIED | FAILED

Claim:
- [One sentence. What did the agent say was done?]

Consumer:
- [Who or what would notice if this were false?]

Evidence accepted:
- [Evidence collected from the consumer end. Include path, URL, command output summary, timestamp, or API readback.]

Evidence rejected:
- [Upstream proxies that do not prove the claim: return code, task-list presence, draft screenshot, self-report, mock-only check.]

Checks run:
- [Concrete checks performed.]

Result:
- [What the consumer-end evidence showed.]

Remaining risk:
- [Any unverified edge or stale assumption.]

Next check if not VERIFIED:
- [Smallest check that would resolve it.]
```

## Status Guide

- `VERIFIED`: evidence comes from the consumer end and matches the claim.
- `PARTIAL`: some narrow claim is proven, but the broad done-claim is not.
- `UNVERIFIED`: evidence is too far upstream, missing, or stale.
- `FAILED`: consumer-end evidence contradicts the claim.

## Common Rejections

Reject these as proof for consumer-facing claims:

- "The task exists in the list."
- "The command returned success."
- "The agent says it wrote it."
- "The local file changed, but the consumer path was not read back."
- "The draft looks right, but the published URL was not opened."
- "The scheduler has a next-run time, but no recent output exists."
