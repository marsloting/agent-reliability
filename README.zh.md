# agent-reliability

**agent 的成功必须由人来定义。**

agent 报"done"和它真的做成了，中间隔着一条鸿沟。这个 repo 是我跑多个 agent 在生产里踩出来的——agent 怎么坏、我怎么防的真实机制，以及可以直接安装进工作流的 skill。

不是链接清单，不是理论。每个机制都是我在跑的，写成你能直接 fork 进自己 agent 工作流的形式。

> 为什么可信：这些来自在生产里跑 agent 被坑出来的，不是读论文读来的。checklist 能抄，判断力抄不走。

## 先从这里开始

你遇到哪类问题，就用哪条。

### agent 说 "done / fixed / sent / scheduled / shipped"

用 [`agent-reliability-guardrails`](skills/agent-reliability-guardrails/)。

它强制从真正会暴露错误的地方拿证据。

### agent 说 "我已经交给另一个 agent 了"

用 [`agent-to-agent-wake-protocol`](skills/agent-to-agent-wake-protocol/)。

它强制接收方回显实际收到的交接行。

### owner 还在跑，或者你在判断要不要接管

用 [`agent-supervisor-guardrails`](skills/agent-supervisor-guardrails/)。

它强制核 owner、核产物、核 ledger，再判断等 / 提醒 / 接管 / 停手。

### agent 说 "批量都过了"

用 [首件检验](mechanisms/first-article-inspection.zh.md)。

它强制第一件验透，再放批。

### agent 说 "我记得契约"

用 [跨域重读](mechanisms/cross-domain-re-read.zh.md)。

它强制跨域边界必须重读当前源。

如果这些规则抓到了你工作流里的虚假 done 或交接丢失，[开一个 failure-pattern issue](https://github.com/marsloting/agent-reliability/issues/new?template=failure-pattern.yml) 贴模式。这个 repo 靠真实事故继续长。

## 楔子 —— 虚假成功（corrupt success）

2026 年的研究给它起了名：**27–78% 的 benchmark"成功"是虚假成功**——agent 绕过授权、伪造确认、传错策略，却照样被判通过（[arXiv 2603.03116](https://arxiv.org/abs/2603.03116)）。

我在它有名字之前就在生产里踩到了。一个 agent 报"已写入"，我信了。下游根本没收到——它写的位置，不是真正被读的那个位置。它没撒谎，真写了。只是那条"写好了"的证据，取自出错点的上游，不是最终被消费的那端。看起来对。验收过了。其实漂了。

解法不是换个更强的模型，是**改变你采证据的位置**。

## 为什么是华语 dev 该看的

英文圈在卷"agent 是什么、框架怎么选"。但 demo 跑通 ≠ 生产能用。从 demo 到生产这条鸿沟，踩过的人知道有多深。这个 repo 讲的就是鸿沟里的坑和防御——华语圈现在满屏入门教程，一线踩坑机制几乎没人写。

## 机制

### [虚假成功](mechanisms/corrupt-success.zh.md)

坏法：agent 报 done，outcome check 被绕。

防御：证据必须取自"claim 为假时错误会暴露的那个位置"。

### [首件检验](mechanisms/first-article-inspection.zh.md)

坏法：批量产出错误同构，抽检抓不住。

防御：第一件对着契约验透再放批。

### [复合衰减](mechanisms/compound-decay.zh.md)

坏法：单步 85% 可靠，8 步只剩 27%。

防御：先数链长；每 3-4 步插验证检查点；检查点 = 回滚单元。

### [跨域重读](mechanisms/cross-domain-re-read.zh.md)

坏法：记忆过期零报错。

防御：记忆是已过期缓存，跨域边界必重读契约。

*这个 repo 随我在生产里踩新坑持续长。star 跟进——它是"还在坏什么"的实时镜像，不是写完就完的成品。每个机制踩到当天我会先发出来：[X @marsloting](https://x.com/marsloting)。*

## 怎么用机制

每个机制末尾有一条规则。复制进你 agent 的 `CLAUDE.md` / `AGENTS.md` 当硬纪律。零安装。

## 可安装 skills

### 1. Done-claim guardrails

当 agent 说它已经 done / fixed / sent / scheduled / shipped 时，用 [`skills/agent-reliability-guardrails/`](skills/agent-reliability-guardrails/) 要求它给 receipt。

### 2. Agent-to-agent wake protocol

当两个桌面 AI agent 互相交接任务时，用 [`skills/agent-to-agent-wake-protocol/`](skills/agent-to-agent-wake-protocol/) 要求接收方回显交接行，证明它真的收到了、读到了正确来源。

### 3. Agent supervisor guardrails

当一个 agent 需要盯另一个长期执行 agent 时，用 [`skills/agent-supervisor-guardrails/`](skills/agent-supervisor-guardrails/) 要求它核 owner、核产物、核 ledger，并在等待、提醒、接管、停手之间做判断。

安装到 Codex：

```bash
git clone https://github.com/marsloting/agent-reliability.git
mkdir -p ~/.codex/skills
cp -R agent-reliability/skills/agent-reliability-guardrails ~/.codex/skills/
cp -R agent-reliability/skills/agent-to-agent-wake-protocol ~/.codex/skills/
cp -R agent-reliability/skills/agent-supervisor-guardrails ~/.codex/skills/
```

如果它抓到一次虚假 done 或 agent 交接丢失，[开一个 failure-pattern issue](https://github.com/marsloting/agent-reliability/issues/new?template=failure-pattern.yml)。我在收集真正会在生产里坏掉的模式。

## 相关

- [product-thinking-pack](https://github.com/marsloting/product-thinking-pack) — 如果你的问题不是 agent 做不稳，而是它把错误的东西做得很认真，先用这套 product-thinking skills 把产品形状定住，再上可靠性检查。

---

English → [README.md](README.md)
