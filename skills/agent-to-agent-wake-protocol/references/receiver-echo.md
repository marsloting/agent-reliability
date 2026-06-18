# Receiver Echo Verification

## Why Echo Matters

A sent message is not the same as a delivered handoff. The receiving agent may be in the wrong chat, the line may be changed by the UI, or the referenced section may not exist yet.

Receiver echo turns the handoff into evidence. The receiver repeats the exact wake line it saw and records what it read.

## Receiver Response Template

The receiving agent should answer with:

```text
Received-Wake:
<exact wake line>

Read:
<source ref and section read>

Decision:
ACCEPTED | BLOCKED | NEEDS-CLARIFICATION

Summary:
<one sentence summary of the source note>

Next:
<what the receiver will do next>
```

## Sender Verification Checklist

Before marking a handoff as verified, check:

- The echoed line matches the line that was sent.
- `from`, `target`, `ref`, `section`, `status`, `action`, and `wake-id` are unchanged.
- The receiver read the intended source note and section.
- The receiver summary matches the source note.
- The requested action is still within the allowed scope.

If any item fails, mark the handoff `BLOCKED` or `SENT-UNVERIFIED`.

## Common Failures

### The receiver cannot find the section

Most likely cause: the wake line was sent before the source note was written.

Fix: write the note, verify the section id exists, then send a fresh wake line.

### The echoed line changed

Most likely cause: the chat or automation path changed characters.

Fix: simplify the line, remove unsafe characters, and resend once.

### The receiver is in the wrong chat

Most likely cause: the target app changed state between selecting the chat and typing the line.

Fix: re-open the intended chat, visually confirm it, and resend once.

### The next step needs human approval

Do not hide that behind a wake line. Mark the handoff `BLOCKED` and ask for approval in plain language.
