# Cross-domain re-read

**Stale memory throws no error. The agent doesn't know that it no longer knows — and neither do you, until a consumer silently drops what it produced.**

## Where it breaks

An agent works in domain A, then produces output for domain B — another workflow's inbox, a schema-validated file, a protocol with a required header. It "remembers" B's format from earlier in the session, or from training-time priors, or from the last time it touched B. It produces confidently. Zero errors.

Except B's contract changed since that memory formed. A field became required. A header format was upgraded. A path moved.

The brutal part: **there is no failure signal anywhere in the producing path.** The memory is internally consistent, the output is well-formed by the *old* contract, the write succeeds. The only place the failure exists is at the consumer — which quietly rejects, misparses, or drops the input. If the consumer is another automated stage, nobody sees it at all. This compounds with [corrupt success](corrupt-success.md): the producing agent will even report "done", truthfully, by its own stale standard.

This is a different animal from [context rot](https://research.trychroma.com/context-rot) — that's degradation as one context grows long. Cross-domain staleness needs no long context: a five-minute-old memory of a contract that changed four minutes ago fails identically. Rot degrades; stale memory just lies still.

## What I hit in production

Multiple agent workflows passing work through file-based handoffs, each file format versioned. One agent wrote a batch of entries into a sister workflow's inbox using the entry format it remembered. The format had been upgraded — a required machine-readable metadata line added. Every entry it wrote parsed as legacy and fell out of the automated pipeline.

No exception. No warning. The downstream stats just came up short, and it took a human noticing "this count looks low" to find a batch of well-written, perfectly obsolete entries. The agent's knowledge wasn't wrong when formed. It expired — and expiry emits no event.

Third time a variant of this bit me, the pattern was undeniable: every incident was producing *across a boundary* from memory.

## The defense

1. **Treat memory of any external contract as a cache with no TTL and no invalidation signal.** Schema, protocol, API shape, file format, directory layout — if it lives outside the current working domain, your memory of it is presumed expired.
2. **Re-read the contract at the boundary, before producing.** Not "recall harder" — actually open the current schema/spec/README of the target. The read costs seconds; the silent mismatch costs a debugging session plus everything the consumer already dropped.
3. **Batch into another domain → [first-article inspection](first-article-inspection.md).** Validate item #1 against the *just-read* contract end-to-end (does the consumer actually accept it?) before producing the rest.
4. **Same mismatch class twice → stop patching, diff the contracts.** A second occurrence means your mental model of the boundary is systematically stale, not occasionally unlucky. Re-read the whole contract and diff it against what you assumed — fixes at instance level just reschedule the third incident.

## The rule — paste into `CLAUDE.md` / `AGENTS.md`

> Memory of any contract outside the current domain (schema, protocol, API, file format) is an expired cache. Before producing cross-domain output, re-read the target's current contract from source — never produce from memory. For batches, validate item #1 against the consumer before the rest.
