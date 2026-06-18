# Wire Format

## Public Wake Line

Use this format:

```text
WAKE from=<sender> target=<receiver> ref=<source-ref> section=<section-id> status=<status-code> action=<read|execute|info> wake-id=<unique-id>
```

Example:

```text
WAKE from=draft-agent target=review-agent ref=shared-note section=article-7 status=READY-FOR-REVIEW action=read wake-id=wake-20260618-001
```

## Field Guide

- `from` is the sending agent alias.
- `target` is the receiving agent alias.
- `ref` is a short pointer to the shared source note, document, issue, or workspace entry.
- `section` is the exact section id the receiver should read.
- `status` is the handoff state, written as uppercase words joined with hyphens.
- `action` is `read`, `execute`, or `info`.
- `wake-id` is a unique id for this wake attempt.

## Wire-Safe Character Rule

The wake line should survive chat boxes, OS automation, browser automation, and copy-paste tools. Keep it boring on purpose.

Allowed characters:

```text
a-z A-Z 0-9 - . / =
```

Use single spaces between fields. Do not put spaces inside field values.

Avoid:

- Underscores.
- Non-English characters.
- Section symbols.
- Emoji.
- Quotation marks.
- Local absolute file paths.
- Full private names or private account labels.

Put natural language in the source note, not in the wake line.

## Good Lines

```text
WAKE from=writer target=reviewer ref=shared-note section=draft-2 status=READY-FOR-REVIEW action=read wake-id=wake-001
```

```text
WAKE from=planner target=builder ref=project-board section=next-step status=READY-TO-EXECUTE action=execute wake-id=wake-002
```

## Bad Lines

```text
WAKE from=writer_agent target=reviewer_agent ref=private-machine-path section=part_2 status=ready action=read wake-id=wake_001
```

Problems: underscores, machine-specific source reference, lowercase vague status, and an underscore in the wake id.

```text
WAKE from=person-name target=private-review-bot ref=client-secret-plan section=today status=READY action=execute wake-id=wake-003
```

Problems: private identity context, private client context, and an action that may be too broad for an unapproved handoff.
