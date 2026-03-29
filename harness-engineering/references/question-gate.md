# Question Gate

## Purpose

Use this file as the shared policy for deciding when an agent may ask the user a question and when it must keep moving.

The default bias is execution, not interruption.

## Interaction Modes

### `DEFAULT`

- This is the normal operating mode.
- Do not ask the user about routine defaults, naming, directory shape, optional subsystems, or whether to keep going.
- Record the smallest credible assumption and continue.
- Child agents never ask the user directly.
- The parent agent may ask the user only when a hard blocker prevents safe progress.

### `PLAN`

- Use this mode for explicit planning or scope-shaping passes.
- Only identity-level hard blockers may become user questions.
- Optional expansion, baseline confirmation, and routine defaults still do not justify a user question.
- Child agents still do not ask the user directly. They return structured blocking state to the parent.

## Blocker Types

Use this taxonomy in handoffs and blocked returns:

- `none`: no blocker.
- `credential`: missing credentials, permissions, secrets, or approvals required to proceed.
- `destructive`: the next action is irreversible or materially risky and user intent is not explicit.
- `identity`: the first runnable artifact, target outcome, or implementation owner is genuinely unclear.
- `conflict`: the available instructions or repo signals conflict and no lowest-risk interpretation is credible.
- `system`: external system failure, unavailable dependency, broken environment, or other execution issue that is not a user-choice question.

## Decision Matrix

| Situation | Child action | Parent action |
|---|---|---|
| Routine default, naming, scaffold size, optional subsystem | Record assumption and continue | Do not ask the user |
| Need another role or more repo context | Return `NEEDS_CONTEXT` | Dispatch the role or provide context |
| Missing credentials or permission | Return `BLOCKED` with `blocking_issue.type: credential` | Ask the user |
| Ambiguous destructive action | Return `BLOCKED` with `blocking_issue.type: destructive` | Ask the user |
| First runnable artifact or owner unclear | Return `BLOCKED` with `blocking_issue.type: identity` | Ask the user only if no smallest credible baseline exists |
| Conflicting instructions or repo signals | Return `BLOCKED` with `blocking_issue.type: conflict` | Ask the user |
| External outage, broken toolchain, flaky dependency | Return `BLOCKED` with `blocking_issue.type: system` and evidence | Retry, defer, or report. Do not ask the user unless they can directly unblock it |

## Parent-Agent Rules

- The parent agent owns every user-facing question.
- Set `interaction_mode: DEFAULT | PLAN` on every handoff.
- Include this file in the artifacts a child should read when the task uses harness orchestration.
- In `DEFAULT`, treat `NEEDS_CONTEXT` as a dispatch or context problem, not a reason to question the user.
- When a question is unavoidable, ask one focused question that targets the hard blocker only.

## Child-Agent Rules

- Never ask the user directly.
- Use `NEEDS_CONTEXT` when another role, another artifact, or more repo context is required.
- Use `BLOCKED` only when a hard blocker stops safe progress.
- Include `interaction_mode` in the return payload.
- Include blocker classification in the return payload. Status-based roles should use `blocking_issue.type`.
