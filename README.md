# agent-reliability

**An agent's success is defined by a human, not the agent.**

Production-tested mechanisms and installable skills for the gap between *"the agent said it worked"* and *"it actually worked."* These are failure modes I hit running multiple agents in production — and the defenses that actually held.

Not a link list. Not theory. Each mechanism is something I run, written so you can fork it into your own agent workflow today.

> Why trust this: these come from running agents in production and getting burned, not from reading papers. You can clone a checklist. You can't clone judgment.

## Start here

Pick the failure mode you are seeing:

| If your agent says... | Use this | What it forces |
|---|---|---|
| "Done", "fixed", "sent", "scheduled", or "shipped" | [`agent-reliability-guardrails`](skills/agent-reliability-guardrails/) | A receipt from the place where failure would show up |
| "I handed it to the other agent" | [`agent-to-agent-wake-protocol`](skills/agent-to-agent-wake-protocol/) | A receiver echo proving the handoff actually landed |
| "It passed the batch" | [First-article inspection](mechanisms/first-article-inspection.md) | Full validation on item #1 before scaling |
| "I remember the contract" | [Cross-domain re-read](mechanisms/cross-domain-re-read.md) | Re-read the current source at every domain boundary |

If one of these catches a false done-claim in your workflow, open an issue with the failure pattern. This repo grows from real breaks.

## The wedge — corrupt success

2026 research finally named it: **27–78% of benchmark "successes" are corrupt** — the agent bypassed authorization, fabricated confirmation, or passed the wrong policy, and still got marked done ([arXiv 2603.03116](https://arxiv.org/abs/2603.03116)).

I hit this in production before it had a name. An agent reported *"written."* Downstream never got it — it wrote to a location nothing reads. It didn't lie. It really wrote. The proof of "done" just came from upstream of where things break, not from the end that consumes the data. Looked correct. Passed review. Drifted silently.

The fix isn't a better model. It's **moving where you collect the evidence**.

## Mechanisms

| Mechanism | What goes wrong | The defense |
|---|---|---|
| [Corrupt success](mechanisms/corrupt-success.md) | Agent reports done; the outcome check gets gamed | Evidence must come from where the error surfaces if the claim is false |
| [First-article inspection](mechanisms/first-article-inspection.md) | Batch errors are homogeneous; spot-checks miss them | Validate item #1 fully against the contract before the batch |
| [Compound decay](mechanisms/compound-decay.md) | 85% per-step reliability → 27% over 8 steps | Count the chain; verified checkpoints every 3–4 steps; checkpoint = rollback unit |
| [Cross-domain re-read](mechanisms/cross-domain-re-read.md) | Stale memory, zero error signal | Memory is an expired cache; re-read the contract at every domain boundary |

*This repo grows as I hit new failure modes in production. Star to follow — it's a live mirror of what's actually breaking, not a finished artifact. I post each mechanism as I hit it: [@marsloting on X](https://x.com/marsloting).*

## How to use the mechanisms

Each mechanism ends with a one-line rule. Paste it into your agent's `CLAUDE.md` / `AGENTS.md` as a hard discipline. No install.

## Installable skills

I turned the highest-friction checks into installable skills:

### 1. Done-claim guardrails

Use [`skills/agent-reliability-guardrails/`](skills/agent-reliability-guardrails/) when an agent says something is done, written, sent, scheduled, shipped, or fixed and you need proof before accepting the claim.

### 2. Agent-to-agent wake protocol

Use [`skills/agent-to-agent-wake-protocol/`](skills/agent-to-agent-wake-protocol/) when two desktop AI agents need to hand off work and you need proof that the receiving agent saw the exact wake line and read the right source note.

Install both for Codex:

```bash
git clone https://github.com/marsloting/agent-reliability.git
mkdir -p ~/.codex/skills
cp -R agent-reliability/skills/agent-reliability-guardrails ~/.codex/skills/
cp -R agent-reliability/skills/agent-to-agent-wake-protocol ~/.codex/skills/
```

Use without installing:

1. For done-claims, open [`agent-reliability-guardrails/SKILL.md`](skills/agent-reliability-guardrails/SKILL.md) and copy the receipt format.
2. For agent handoffs, open [`agent-to-agent-wake-protocol/SKILL.md`](skills/agent-to-agent-wake-protocol/SKILL.md) and copy the wake line format.
3. Require every claim or handoff to end with a verifiable receipt, not a self-report.

Start with the worked example:

- [Scheduled task silent death](skills/agent-reliability-guardrails/references/worked-example-scheduled-task-silent-death.md) — seven visible scheduled jobs, empty task bodies, no output for days, no error signal.

CTA: if this catches one false done-claim or dropped handoff in your workflow, open an issue with the pattern. I am collecting the mechanisms that actually fail in production.

## Related

- [vectara/awesome-agent-failures](https://github.com/vectara/awesome-agent-failures) — a catalog of *what* went wrong (news links). This repo is the opposite: the *mechanisms* that stop it.
- [ai-boost/awesome-harness-engineering](https://github.com/ai-boost/awesome-harness-engineering) — the scaffolding around agents.

---

中文版 → [README.zh.md](README.zh.md)
