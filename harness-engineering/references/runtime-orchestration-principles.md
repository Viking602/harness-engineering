# Runtime Orchestration Principles

## Sources

- Article: `Harness design for long-running application development`
- Author: Prithvi Rajasekaran
- Published: 2026-03-24
- URL: `https://www.anthropic.com/engineering/harness-design-long-running-apps`

## Core Thesis

Long-running agent work fails for predictable reasons: context degrades over time, builders under-scope or lose coherence, and models grade their own work too generously. A runtime harness should therefore separate planning, implementation, and evaluation, then force the work through explicit contracts and external verification.

For a compact synthesis that combines this article with OpenAI's repo-shaping lessons, read `./long-running-harness-reference.md`.

## When To Add Runtime Roles

Add planner, generator, or evaluator roles only when the task needs them.

- Add a planner when the user prompt is short but the build needs a richer spec or decomposition.
- Add a generator when one bounded role should own implementation against a clear contract.
- Add an evaluator when local tests alone are not enough, the output quality is subjective, or the builder repeatedly misses real defects.
- Collapse roles when the work is short, the criteria are obvious, or the model can already handle the task reliably without extra scaffolding.

## Minimal Runtime Contract

Define these items before recommending a multi-role loop:

1. Control plane: who sets priorities, acceptance criteria, budget, stop conditions, and escalation rules.
2. Role boundaries: what planner, generator, and evaluator each own, and what they do not own.
3. Shared artifacts: spec, sprint or iteration contract, handoff notes, QA report, and evidence log.
4. Verification surface: which tools observe the real system, such as browser automation, API checks, logs, metrics, traces, or database queries.
5. Recovery policy: retry limits, pivot conditions, context-reset rules, and human handoff triggers.
6. Routing-doc sync: which files map the current project surface, and how structure changes will be reflected there before the sprint closes.

## Planner Guidance

The planner should expand underspecified prompts into a buildable plan without over-specifying implementation details that may later prove wrong.

- Focus on product intent, scope, and testable deliverables.
- Keep implementation freedom for downstream roles unless a hard constraint is already known.
- Write a spec that can survive handoff to another role or another session.

## Generator Guidance

The generator owns implementation against an agreed contract.

- Work in bounded chunks when the task is long or brittle.
- Build against the current contract instead of free-form improvisation.
- Emit concrete handoff state: what changed, what passed, what is risky, and what the evaluator must check next.
- Update or explicitly defer required routing-doc changes when the sprint moves paths, commands, or major docs.

## Evaluator Guidance

The evaluator should judge the real artifact, not just the builder's explanation.

- Exercise the running system through external tools whenever possible.
- Grade against explicit criteria or thresholds.
- Return findings that are specific enough to act on without more investigation.
- Fail the iteration if a required criterion falls below threshold.
- Fail the iteration when `AGENTS.md`, `docs/index.md`, or equivalent routing docs no longer match the changed project surface.

## Evidence Template

Prefer artifacts like these over narrative summaries:

- commands run
- failing and passing checks
- screenshots or DOM observations
- log, metric, or trace excerpts
- exact bug reproduction steps
- contract items passed or failed

## Recovery Loop

A minimal loop is:

1. Plan or refresh the contract.
2. Implement the next bounded chunk.
3. Observe the real system.
4. Record findings with evidence.
5. Repair or pivot.
6. Re-verify.
7. Escalate if the retry budget is spent or the task needs human judgment.

## Runtime Anti-Patterns

- One role writes, grades, and approves its own work.
- The evaluator reads code but never touches the running system.
- The team keeps acceptance criteria in chat instead of versioned artifacts.
- Handoffs are free-form summaries with no contract or evidence.
- Retries continue forever with no stop or escalate rule.
- The harness keeps old roles or resets that no longer add value.

## Starter Checklist

1. Decide whether one agent is enough before adding more roles.
2. Define the control-plane owner and the stop conditions.
3. Write the spec or iteration contract in a durable artifact.
4. Pick the runtime tools the evaluator will use.
5. Define pass or fail thresholds.
6. Require evidence in every handoff.
7. Set retry and escalation rules before the run starts.
8. Remove orchestration layers that stop paying for themselves.
