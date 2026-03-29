# Harness Engineering Principles

## Source

- Article: `工程技术：在智能体优先的世界中利用 Codex`
- Author: Ryan Lopopolo
- Published: 2026-02-11
- URL: `https://openai.com/zh-Hans-CN/index/harness-engineering/`

## Core Thesis

The article argues that once coding agents produce most changes, engineering leverage shifts away from typing code and toward shaping the environment around the agent. The repository, tooling, guardrails, and feedback loops become the main product of the engineering team because they determine whether the agent can act reliably over long horizons.

## Transferable Principles

### 1. Start With An Empty Repo, But Not With An Empty Harness

An agent can generate a large codebase quickly, but only if the initial repository structure, package layout, CI shape, and working rules are already legible enough to build on. Early progress usually stalls because the environment is underspecified, not because the model is incapable.

### 2. Keep Humans In The Control Plane

Humans still decide priorities, intent, and acceptance criteria. The agent executes implementation, review responses, retries, and repair loops. If the agent repeatedly fails, treat that as a signal that the harness is missing documentation, tooling, or constraints.

### 3. Optimize The App And Tooling For Machine Readability

Agent-friendly engineering is not only about source code. UI state, logs, metrics, traces, review feedback, and local dev workflows must be available in a form the agent can query and reason about directly. A task becomes much easier when the agent can reproduce the issue, inspect the runtime, and verify the fix without manual translation.

### 4. Use AGENTS.md As A Map

The article is explicit that a giant `AGENTS.md` failed. The durable pattern is a short routing file plus a structured `docs/` tree that holds the real system knowledge. This supports progressive disclosure and reduces context waste.

### 5. Version The Knowledge The Agent Needs

Architecture notes, product specs, plans, generated references, and quality rubrics should live in the repo. Hidden knowledge in chat, Google Docs, or people's heads is invisible to the agent until someone converts it into versioned local artifacts.

### 6. Encode Boundaries Mechanically

The more autonomous the agent is, the more important it becomes to enforce dependency direction, module layering, naming rules, logging conventions, and reliability constraints with code rather than prose. Guardrails act as multipliers when they are machine-enforced.

### 7. Prefer Inspectable Building Blocks

The article repeatedly favors transparent, locally understandable abstractions over opaque dependencies. Sometimes a small in-house implementation is cheaper than asking the agent to reason around third-party behavior it cannot see clearly.

### 8. Increase Throughput By Shrinking Wait States

High agent throughput changes the tradeoff around merges and retries. Waiting on slow gates becomes more expensive than fixing problems quickly after the fact. This does not mean "skip quality"; it means push quality into fast local verification, retry loops, and targeted repair.

### 9. Treat Drift As A Continuous Garbage-Collection Problem

Agent-generated code will reproduce both good and bad local patterns. The answer is not periodic manual cleanup alone. The answer is to codify taste, run cleanup loops, detect stale docs and structural drift, and merge small corrections continuously.

## Anti-Patterns

- Giant instruction blobs that crowd out task-specific context
- Knowledge that exists only in chat, meetings, or external docs
- Architecture rules enforced only by review comments
- Opaque dependencies the agent cannot inspect or model well
- Human-only QA loops that require manual translation of every failure
- Large deferred cleanup projects instead of constant small corrections

## Starter Checklist

Use this checklist when applying the skill to a real repository:

1. Write or trim `AGENTS.md` so it routes to deeper docs instead of trying to contain everything.
2. Create a versioned docs layout for architecture, product knowledge, plans, generated references, and standards.
3. Add indexes or entry points so the agent can discover where to read next.
4. Move recurring chat guidance into docs, tests, scripts, or config.
5. Add structural checks for architecture and naming rules that the agent tends to violate.
6. Make runtime verification accessible through local tools, logs, screenshots, metrics, or traces.
7. Define measurable acceptance criteria for important tasks.
8. Add a recurring cleanup loop for stale docs, drift, and low-grade code entropy.

## Example Knowledge Layout

Use this as a shape, not as a mandatory structure:

```text
AGENTS.md
ARCHITECTURE.md
docs/
  architecture/
    index.md
    core-beliefs.md
  product/
    index.md
  plans/
    active/
    completed/
    tech-debt.md
  generated/
  references/
```

The important properties are:

- a short top-level map
- stable locations for durable knowledge
- separate homes for active plans, completed history, and generated material
- enough indexing that the agent can discover the next document without guessing

## AGENTS.md Checklist

Keep `AGENTS.md` lean. It should usually do four jobs:

1. State the repository's top-level rules and non-negotiable constraints.
2. Point to the most important deeper docs.
3. Explain how to validate changes locally.
4. Explain where plans, generated references, and long-lived decisions live.

## What Not To Cargo-Cult

The article describes OpenAI's own setup, including a fully agent-generated codebase, strong internal tooling, custom linting, and high pull-request throughput. Do not copy those exact numbers or folder names blindly. Carry over the principles that generalize:

- local truth over hidden context
- progressive disclosure over giant prompts
- machine-enforced guardrails over repeated human reminders
- local verification over trust-based edits
- continuous cleanup over occasional rescue missions
