# Long-Running Harness Reference

Use this reference when the task is likely to run for a long time, span multiple sessions, need multiple repair loops, or depend on runtime verification that local unit tests alone cannot cover.

## Sources

- OpenAI: `https://openai.com/zh-Hans-CN/index/harness-engineering/`
- Anthropic: `https://www.anthropic.com/engineering/harness-design-long-running-apps`

## What To Carry Forward

### 1. Keep Humans In The Control Plane

Humans still own priorities, acceptance criteria, budget, stop conditions, and escalation. Long-running autonomy does not remove control-plane work. It increases the value of making that work explicit.

### 2. Keep Working State In Versioned Files

Long tasks degrade when the next agent must reconstruct intent from chat. Keep the active spec, current contract, latest handoff, QA notes, and evidence in files the next run can read directly.

Minimum artifact set:

- spec or plan
- current contract for the next bounded chunk
- implementation handoff
- QA report
- evidence log or concrete verification record

### 3. Split Roles Only When They Add Lift

Do not cargo-cult planner, generator, and evaluator on every task.

- Add a planner when the prompt is too short for reliable implementation.
- Add an evaluator when self-review is weak, runtime behavior matters, or the output quality is subjective.
- Collapse back to one agent when the task is short, obvious, and locally verifiable.

Each extra role is a claim that the base model still needs help in that part of the loop.

### 4. Force The Work Through Explicit Contracts

The generator should not improvise against a vague prompt for hours. Define what the next chunk will deliver, how it will be verified, what files it may touch, and when it must stop or escalate.

Good contracts are:

- bounded
- testable
- artifact-backed
- narrow enough for an evaluator to grade without guesswork

See `./examples/sprint-contract-template.md` for a complete contract template with fields, examples, and sign-off procedures.

### 5. Judge Through External Verification

Do not let the builder approve its own work by narration alone. Long-running harnesses improve when the evaluator touches the real system through external surfaces such as:

- browser automation
- API checks
- database queries
- logs, metrics, and traces
- screenshots or DOM observations

The evaluator should return evidence and explicit pass or fail judgment, not a vague confidence statement.

See `./examples/evaluator-checklist.md` for task-specific evaluation criteria, verification methods, and evidence collection templates.

### 6. Use Thresholds And Stop Conditions

Long runs drift when the harness never says "fail", "pivot", or "stop". Define retry budgets, pass thresholds, and escalation triggers before the run starts.

Examples:

- fail when a required criterion falls below threshold
- escalate after N failed repair loops
- stop when the contract cannot be made testable
- stop when routing docs no longer match the changed project surface

### 7. Manage Context Decay Deliberately

Two patterns matter:

- Keep one continuous run when the model can still stay coherent and compaction is enough.
- Reset context and hand off through files when the model starts under-scoping, wrapping up early, or losing track of previous decisions.

Do not romanticize either approach. Choose the cheaper one that preserves coherence.

### 8. Keep Routing Docs Fresh During The Run

OpenAI's repo-shaping lesson and Anthropic's contract lesson meet here: if the task changes the project surface, `AGENTS.md`, `docs/index.md`, and similar entrypoints must be updated in the same pass, not "afterwards".

### 9. Treat Harness Pieces As Load-Bearing Until Proven Otherwise

Every planner, evaluator, reset, or extra handoff exists because you think the model cannot yet do that part reliably alone. Re-test those assumptions as models improve. Remove harness pieces that no longer add clear lift.

**Simplification signals:**
- The model sustains longer coherent runs without under-scoping
- Sprint contracts become ceremonial (immediate agreement without negotiation)
- Evaluators find nothing meaningful across multiple iterations
- Context resets add more overhead than value
- Repair loops converge immediately

**Real example with Opus 4.6:**
- Removed sprint contracts (model plans more carefully upfront)
- Moved to single-pass evaluation (model sustains longer tasks)
- Kept evaluator for "last mile issues"
- Result: 6hr/$200 → 4hr/$124 with same quality

See `../SKILL.md` "When To Simplify The Harness" section for the full decision tree.

### 10. Convert Repeated Failure Into Durable Guardrails

If the same failure shows up across runs, do not keep fixing it by reminder alone. Move it into:

- a clearer contract template
- an evaluator fail condition
- a structural check or lint
- a routing-doc rule
- a maintenance or doc-gardening pass

## What Belongs In References Vs Hard Constraints

Put these in hard constraints:

- Required handoff fields
- Evaluator fail conditions
- Routing-doc invariants
- Dispatch-gate requirements
- Evidence requirements

Keep these in references:

- When to add or remove runtime roles
- When context resets beat compaction
- Examples of verification surfaces
- Simplification heuristics as models improve
- Article-specific rationale and non-goals
- **Sprint contract templates** - see `./examples/sprint-contract-template.md`
- **Evaluator checklists** - see `./examples/evaluator-checklist.md`

## Quick Decision Table

| Signal | Recommended move |
| --- | --- |
| Prompt is short but implementation is broad | Add planner |
| Builder keeps missing runtime bugs | Add evaluator with external tools |
| Task is short and locally obvious | Stay single-agent |
| Model loses coherence over time | Reset context and hand off through files |
| Evaluator keeps finding nothing meaningful | Remove or defer evaluator |
| Surface changed during the run | Update routing docs in the same pass |
| Model sustains long coherent runs reliably | Consider simplifying harness |

## What Not To Cargo-Cult

- OpenAI's exact repo layout, throughput numbers, or custom lint inventory
- Anthropic's exact sprint shape, number of iterations, or tool stack
- any harness layer that no longer produces clear lift on the current model

Carry over the general rules instead:

- local files beat hidden context
- explicit contracts beat vague prompts
- external verification beats self-approval
- hard fail conditions beat soft suggestions
- periodic simplification beats inherited harness sprawl
