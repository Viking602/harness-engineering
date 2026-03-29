# Subagent Dispatch Playbook

## Purpose

Use this reference when the main `harness-engineering` skill has already decided that planner, generator, or evaluator roles are worth the extra orchestration cost. The parent agent stays responsible for sequencing, integration, and final judgment.

Role behavior, return shape, and read/write mode belong in the corresponding `agents/*.toml` files. This document only covers dispatch choice and parent-agent handoff.

## Control Plane

- The parent agent owns all dispatch decisions and direct subagent invocation.
- Planner, dispatch gate, generator, evaluator, doc-gardener, and domain-specific implementation agents are leaf roles for this workflow.
- Do not use `codex exec`, `codex review`, or nested Codex CLI sessions from child agents to create more agents or review passes.
- When a child agent discovers that another role is required, it should stop and return `NEEDS_CONTEXT` or `BLOCKED` with the requested next role.

## Question Gate

- Follow `./question-gate.md` as the shared interaction policy.
- The parent agent owns every user-facing question. Child agents never ask the user directly.
- Set `interaction_mode: DEFAULT | PLAN` on every handoff.
- In `DEFAULT`, do not ask the user about routine defaults, optional scope expansion, naming, or baseline confirmation. Record assumptions and continue.
- In `PLAN`, only hard blockers that affect the identity of the first runnable artifact or implementation owner may become user questions.
- Use the blocker taxonomy from `./question-gate.md`: `none | credential | destructive | identity | conflict | system`.
- Treat `NEEDS_CONTEXT` as a parent-side dispatch or context problem, not a user question by default.
- Treat `BLOCKED` with `credential`, `destructive`, `identity`, or `conflict` as potentially user-facing. Treat `BLOCKED` with `system` as evidence to retry, defer, or report unless the user can directly unblock it.

## Default Role Mapping

### Planner

Preferred agent types:

1. `harness-planner` when this repo's Codex agent files are installed
2. `research-analyst`
3. `product-manager`

Choose among these based on where the ambiguity lives: repo-specific planning context, technical decomposition, or product scope.

### Dispatch Gate

Preferred agent types:

1. `harness-dispatch-gate` when this repo's Codex agent files are installed
2. the parent agent using this playbook directly

Run this gate before any stack-owned code-writing step when the user or repo makes the language, runtime, framework, or platform explicit.

The gate must decide:

- detected stack ownership
- matching domain-specific implementation agent candidates
- whether domain-agent dispatch is required
- whether the current environment can dispatch the chosen domain agent
- whether a generic generator is allowed, and why

### Generator

Preferred agent types:

1. a matching domain-specific implementation agent when the user or repo makes the language, runtime, framework, or platform explicit
2. `harness-generator` when this repo's Codex agent files are installed and no stronger domain match exists
3. `worker`

Choose among these based on execution shape: direct language ownership first, repo-specific custom generator second, generic bounded implementation last.

Use a generic generator only after the dispatch gate explicitly allows it.
Count both built-in domain agents exposed by the current environment and any installed custom agent files.
If the current environment supports subagent dispatch, use the chosen matching domain agent for the implementation step. If it does not, report the dispatch limitation explicitly before continuing with a generic path.

Examples:

- Rust -> `rust-engineer`
- Python -> `python-pro`
- Go -> `golang-pro`
- TypeScript -> `typescript-pro`
- JavaScript -> `javascript-pro`
- C# or .NET -> `csharp-developer`, `dotnet-core-expert`, `dotnet-framework-4.8-expert`
- Java -> `java-architect`, `spring-boot-engineer`
- Kotlin -> `kotlin-specialist`
- Swift -> `swift-expert`
- Flutter -> `flutter-dev`
- React Native -> `react-native-dev`
- Frontend-only web app -> `frontend-developer`, `react-specialist`, `nextjs-developer`, `vue-expert`, `angular-architect`
- Full-stack app with a clear web stack -> `fullstack-developer`

### Evaluator

Preferred agent types:

1. `harness-evaluator` when this repo's Codex agent files are installed
2. `reviewer`
3. `qa-expert`
4. `browser-debugger`

Choose among these based on the evidence surface: general review, acceptance coverage, or browser and runtime inspection.

## Dispatch Order

Use the smallest sequence that fits the task:

1. `planner -> dispatch gate -> generator` for underspecified work where implementation ownership still needs to be resolved
2. `dispatch gate -> generator -> evaluator` when scope is clear but implementation needs independent judgment
3. `planner -> dispatch gate -> generator -> evaluator` for long-running or failure-prone work

Skip the gate only when the implementation surface is not stack-owned or when the task is routing-doc-only, read-only, or clearly not entering a code-writing phase.
Do not dispatch every role by default. Each extra role needs a reason.

## Bootstrap Bias

- For empty or near-empty repos, if the user already named the stack and target artifact, the parent agent should pick the smallest credible baseline and start work.
- Do not stop to present template menus such as "minimal vs standard vs production-ready" unless the missing choice would change the implementation owner or create a high-cost wrong turn.
- Separate identity questions from expansion questions. Identity questions change the first runnable artifact or implementation owner. Expansion questions only widen scope.
- Do not turn expansion questions into follow-up prompts during the first scaffold pass. Pick the narrowest runnable baseline, record the assumption, and continue.
- Once the identity of the first runnable artifact is clear, do not ask the user to confirm that same minimal baseline. Announce it as the working assumption and proceed.
- Prefer recording assumptions in the handoff over asking the user to approve routine scaffold choices.
- Ask only when the request is still missing an identity-level blocking dimension, for example the target artifact itself is unclear or the repo contains conflicting stack signals.

If a task changes structure, entrypoints, or routing docs, treat doc synchronization as part of the generator or repair pass instead of a cleanup someday task.
Keep the routing split stable: `AGENTS.md` is the mandatory short root map, and `docs/index.md` is the deeper docs map when the repo has durable docs.

## Codex Agent Files

If you want repo-specific Codex custom agents instead of generic built-ins, use these files as the source of truth and install copies into `~/.codex/agents`:

- `../codex/agents/harness-planner.toml`
- `../codex/agents/harness-dispatch-gate.toml`
- `../codex/agents/harness-generator.toml`
- `../codex/agents/harness-evaluator.toml`
- `../codex/agents/harness-doc-gardener.toml` for optional maintenance passes that refresh routing docs after tree changes or merges

If the current Codex session does not resolve a newly installed custom `agent_type`, fall back to the built-in mapping for that run and retry the custom type in a fresh session.

If a matching domain-specific built-in agent exists, prefer it over `harness-generator` for the implementation phase of repo initialization. Keep `harness-generator` for repo-specific orchestration only when the gate says a generic generator is acceptable.

## Optional Maintenance Helper

- Use `harness-doc-gardener` after structure-heavy changes, command renames, major doc moves, or merges that make routing docs suspect.
- Keep it off the critical path for small tasks where the generator already updated `AGENTS.md` and `docs/index.md` in the same pass.
- Treat it as a leaf role. If it discovers the need for another agent, it must return `BLOCKED` or `NEEDS_CONTEXT` to the parent instead of opening nested CLI sessions.

## Anti-Patterns

- dispatching a planner when the parent already has a clear contract
- giving the generator vague ownership across the whole repo
- asking all three roles to read everything in the repo
- keeping subagents open after their output is integrated

## Parent-Agent Dispatch Minimum

For any of the three roles, the parent agent should provide:

1. objective
2. artifacts to read first
3. scope boundaries
4. required output
5. verification or grading criteria
6. escalation condition
7. leaf-role rule: do not open `codex exec`, `codex review`, or other nested Codex CLI sessions; return control to the parent agent instead
8. `interaction_mode: DEFAULT | PLAN`
9. question-gate path: `./question-gate.md`

If the task changes the repo surface, add:

10. which routing docs must change
11. how the evaluator should verify routing-doc freshness

For the dispatch gate, also provide:

12. explicit language, runtime, framework, or platform signals
13. whether the task is entering a stack-owned code-writing phase now
