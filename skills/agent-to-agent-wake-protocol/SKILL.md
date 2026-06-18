---
name: agent-to-agent-wake-protocol
description: Coordinate two desktop AI agents that do not share an API by using a short wire safe wake line plus receiver echo verification. Use when a user wants one AI app to wake brief or route work to another AI app when manual copy paste routing is causing dropped tasks or when the sender needs evidence that a handoff actually landed. Keeps handoffs generic privacy scrubbed and consumer end verified.
---

# Agent-to-Agent Wake Protocol

## Overview

Use this skill when two desktop AI agents need to collaborate but cannot talk through a shared API. The method is simple: write the handoff note first, send one short wake line to the other agent, then require the receiver to echo the exact line before treating the handoff as delivered.

This is for people running real work across more than one AI app. The goal is not to make an elaborate multi-agent system. The goal is to stop losing context, stop relying on vague "I sent it" claims, and make agent handoffs auditable enough to trust.

## When To Use

Use this skill for:

- Passing a draft from one AI app to another for review.
- Asking a second AI app to continue a task without manual copy-paste of the whole context.
- Running a two-agent writing, thinking, research, or review loop.
- Verifying that a wake message actually landed in the receiving app.
- Recovering from a missed handoff, stale chat, or ambiguous "continue from the other agent" instruction.

Do not use it for secrets, payments, legal approvals, account changes, production releases, or any action where a human must explicitly approve the next step.

## Core Rule

Write the source note before sending the wake line.

A wake line is a pointer, not the full brief. The receiving agent should be able to open the referenced note, section, issue, document, or shared workspace entry immediately. If the referenced material does not exist yet, do not wake the other agent.

## Handoff Workflow

1. Write the handoff note in a place both agents can read.
2. Give the note a stable short reference and section id.
3. Create a wire-safe wake line using `references/wire-format.md`.
4. Type the wake line into the receiving agent's chat using OS automation, browser automation, or a careful manual paste.
5. Ask the receiving agent to echo the exact wake line and summarize what it read.
6. Verify the echo before treating the handoff as delivered.

If the receiver does not echo the line, the wake is not verified. If the receiver echoes a different line, treat it as a failed handoff and correct it before continuing.

## Privacy Rules

Before sending or publishing a wake line, remove:

- Real names, private account names, client names, and internal team labels.
- Local absolute file paths and machine-specific app details.
- Secrets, tokens, payment details, personal emails, and private identifiers.
- Internal project names that would reveal the operator, company, or client.

Keep the mechanics. Strip the identity.

## Standard Statuses

Use these status labels in your own handoff notes:

- `READY-TO-WAKE` means the source note exists and can be read now.
- `SENT-UNVERIFIED` means the wake line was sent but no receiver echo is recorded.
- `RECEIVER-ECHO-VERIFIED` means the receiver echoed the exact line and understood the source note.
- `BLOCKED` means the receiver could not read the source, the line changed in transit, or the next step needs human approval.

## Handling Races

Sometimes the target app is busy, a human is typing, or automation tries to click while the app is changing state. If that happens, do not keep retrying blindly.

Use this recovery pattern:

1. Wait until the target app is idle.
2. Re-read the current app state or visually confirm the right chat is open.
3. Retry the wake once.
4. If it still fails, write a short failed-wake note and ask the user or sender to intervene.

The point is reliability, not brute force.

## References

- `references/wire-format.md` explains the wake line format and safe character rules.
- `references/receiver-echo.md` explains how the receiver proves what it actually received.
