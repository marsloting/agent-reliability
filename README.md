# agent-reliability

**An agent's success is defined by a human, not the agent.**

Production-tested mechanisms for the gap between *"the agent said it worked"* and *"it actually worked."* These are failure modes I hit running multiple agents in production — and the defenses that actually held.

Not a link list. Not theory. Each mechanism is something I run, written so you can fork it into your own agent workflow today.

> Why trust this: these come from running agents in production and getting burned, not from reading papers. You can clone a checklist. You can't clone judgment.

## The wedge — corrupt success

2026 research finally named it: **27–78% of benchmark "successes" are corrupt** — the agent bypassed authorization, fabricated confirmation, or passed the wrong policy, and still got marked done ([arXiv 2603.03116](https://arxiv.org/abs/2603.03116)).

I hit this in production before it had a name. An agent reported *"written."* Downstream never got it — it wrote to a location nothing reads. It didn't lie. It really wrote. The proof of "done" just came from upstream of where things break, not from the end that consumes the data. Looked correct. Passed review. Drifted silently.

The fix isn't a better model. It's **moving where you collect the evidence**.

## Mechanisms

| Mechanism | What goes wrong | The defense |
|---|---|---|
| [Corrupt success](mechanisms/corrupt-success.md) | Agent reports done; the outcome check gets gamed | Evidence must come from where the error surfaces if the claim is false |
| First-article inspection *(coming)* | Batch errors are homogeneous; spot-checks miss them | Validate item #1 fully against the contract before the batch |
| Compound decay *(coming)* | 85% per-step reliability → 27% over 8 steps | Budget reliability per step; don't chain blindly |
| Cross-domain re-read *(coming)* | Stale memory, zero error signal | Treat memory as cache; re-read the contract at every domain boundary |

*This repo grows as I hit new failure modes in production. Star to follow — it's a live mirror of what's actually breaking, not a finished artifact.*

## How to use

Each mechanism ends with a one-line rule. Paste it into your agent's `CLAUDE.md` / `AGENTS.md` as a hard discipline. No install.

## Related

- [vectara/awesome-agent-failures](https://github.com/vectara/awesome-agent-failures) — a catalog of *what* went wrong (news links). This repo is the opposite: the *mechanisms* that stop it.
- [ai-boost/awesome-harness-engineering](https://github.com/ai-boost/awesome-harness-engineering) — the scaffolding around agents.

---

中文版 → [README.zh.md](README.zh.md)
