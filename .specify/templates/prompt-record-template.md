# Prompt Record: [COMMAND] — [TIMESTAMP]

**Command**: `/speckit.[command]` | **Feature**: `[###-feature-name]` | **Branch**: `[###-feature-name]`

**Timestamp**: [YYYY-MM-DDTHH:MM:SS±HH:MM] (system clock, user's local timezone)

**Command Template**: [e.g., `.specify/templates/commands/plan.md`] @ [short hash]

**Note**: This template is filled in by every `/speckit.*` command (built-in or custom) as its **first step**, before any other work. See the "Spec-Kit Prompt Recording & Traceability" section of the constitution for the governing rules.

<!--
  ACTION REQUIRED: Save the filled-in record to:

    specs/[###-feature-name]/prompts/[command]/[YYYYMMDDTHHMMSS±HHMM].md

  - [command]: the command name without the `speckit.` prefix (e.g., specify, plan, ia).
  - File name: the timestamp with no colons. Lexical order equals chronological order.
  - Get the timestamp from the system clock (e.g., `date +"%Y-%m-%dT%H:%M:%S%z"`).
    Never estimate it.
  - Same-second collision: append `-2`, `-3`, ... to the file name.
  - Never overwrite an existing record. Re-running a command creates a new file.
  - Never paste the command template's text. Reference its path and hash above.
  - Delete this comment block from the saved record.
-->

## Prompt

<!--
  The user's exact input to the command (what was passed as $ARGUMENTS), verbatim.
  If the command was invoked with no arguments, write "(none)".
-->

[Verbatim user input]

## Intent

<!-- 1-3 lines. What the user asked for, in the agent's own words. -->

[Brief summary of the request]

## Clarifications

<!-- OPTIONAL: Remove this section if the agent asked no follow-up questions. -->

- Q: [question the agent asked] → A: [user's answer]

## Artifacts Touched

<!--
  Every file this invocation created, updated, or deleted, with its status.
  The record itself is excluded. Fill this in at the END of the command run.
-->

- `specs/[###-feature-name]/[file]` ([created | updated | deleted])
