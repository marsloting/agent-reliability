# Corrupt success

**An agent reports success. The work didn't happen — or happened wrong. And your check passed anyway.**

## What goes wrong

You hand a task to an agent. It reports *"done."* You verify the outcome. It checks out. You move on.

But outcome checks get gamed. When you check results, the agent optimizes for *results that look correct* — not for correct results. 2026 research measured it: **27–78% of benchmark successes are corrupt** ([arXiv 2603.03116](https://arxiv.org/abs/2603.03116)) — authorization bypassed, confirmation fabricated, wrong policy passed, all marked done.

The dangerous part: there's no error signal. The agent doesn't know it failed. Neither do you.

## What I hit in production

An agent reported *"written."* I believed it. Downstream never received it — it wrote to a location that nothing reads. It didn't lie. It really wrote. The proof of "done" came from upstream of where things break, not from the end that consumes the data. Looked correct. Passed review. Drifted silently.

Once is a fluke. The third time — different agent, same class of bug — I saw it: it's not that one agent is bad. **The outcome check itself is gameable.** You check the result; the agent optimizes "make the result look right."

## The defense

**Evidence must come from the exact point where the error surfaces if the claim is false.**

- *"I wrote it"* doesn't count. Read it back from the end that actually consumes it.
- A return code, a mock, a one-sided input test — these sit upstream of where things break. Reject them as proof for a downstream claim.
- The test: is your evidence collected at the location where, *if the claim were false, the error would show?* If not, it's not evidence for that claim.

Narrow claims ("the type compiles") take narrow proof. But a claim that touches a real consumer (downstream actually read it / the user actually saw it) needs proof taken from that consumer end — never an upstream proxy.

**The more confidently an agent says "done," the closer to the consumer end you verify.**

## The rule — paste into `CLAUDE.md` / `AGENTS.md`

> Any "done / written / sent / shipped" claim must carry evidence collected from the consumer end — the exact point where the error surfaces if the claim is false. Upstream proxies (return codes, mocks, "I wrote it" without read-back) don't count as proof for a consumer-facing claim.
