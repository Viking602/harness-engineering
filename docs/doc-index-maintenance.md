# Project Index Maintenance

## Sources

- OpenAI: `https://openai.com/zh-Hans-CN/index/harness-engineering/`
- Anthropic: `https://www.anthropic.com/engineering/harness-design-long-running-apps`
- Backstage TechDocs: `https://backstage.io/docs/next/features/techdocs/creating-and-publishing/`

## Core Point

Routing docs are not commentary. They are executable promises about where the current project surface lives.

If `AGENTS.md` or `docs/index.md` points to stale paths, stale commands, or stale current-doc entrypoints, the harness stops being trustworthy.

`AGENTS.md` is the fixed root entrypoint. `docs/index.md` is the deeper docs map when the repository has durable docs.

## What The Sources Add

### OpenAI

- Keep `AGENTS.md` short and map-like.
- Treat `docs/` as the record system.
- Use `docs/index.md` as the handoff from the short root map into the deeper docs tree.
- Version plans and other durable artifacts.
- Use lint or CI to verify that docs are current, cross-linked, and structurally sound.
- Run doc-gardening passes to repair drift.

### Anthropic

- Keep the active contract, handoff, and QA state in files.
- Make the generator build against the current contract.
- Make the evaluator fail the pass when required criteria are not met.
- This applies to routing docs too: if the sprint changes the project surface, those files must be updated before the sprint is done.

### Backstage

- Keep docs close to the code.
- Maintain a real `docs/index.md`.
- If you add more docs pages, update the navigation in the same change.

## Trigger Conditions

Update routing docs in the same pass when a task changes any of these:

- source roots or major directories
- current spec, contract, handoff, or QA paths
- run commands or verification entrypoints
- architecture maps or other "start here" files
- current code surfaces that a future agent must inspect first

Prefer explicit repo-relative or file-relative paths in routing docs so agents can verify them without guesswork.

If a repo already has or now needs a `docs/` tree, require `docs/index.md` instead of scattering deep-entry guidance across multiple files.

## Minimum Invariants

1. A root `AGENTS.md` exists.
2. Every path named in `AGENTS.md` exists.
3. If the repo has durable docs, a `docs/index.md` exists.
4. Every critical path named in `docs/index.md` exists.
5. New required entrypoints appear in `docs/index.md` in the same change that introduces them.
6. Moved or deleted paths are removed from routing docs in the same change.
7. A structure-changing task does not pass until an evaluator or maintenance pass confirms routing-doc freshness.

## Practical Pattern

Use a three-part rule:

1. Planner:
   Mark which routing docs must change if the task touches structure or commands.
2. Generator:
   Update the docs in the same pass as the code.
3. Evaluator:
   Fail the task when routing docs are stale or incomplete.

For repos that change often, add a maintenance pass with `harness-doc-gardener` or an equivalent doc-gardening agent.

## Agent Review

Use the responsible agent, or a dedicated maintenance pass, to do two things:

1. Fail on stale paths referenced by `AGENTS.md` or `docs/index.md`.
2. Fail when required project surfaces are missing from the index.

## Recommended Contract Language

When the task can move the project surface, add these requirements to the contract:

- `AGENTS.md` and `docs/index.md` must reflect the changed tree before the task closes.
- The evaluator or maintenance pass must confirm routing-doc freshness.
- The generator handoff must say which routing docs changed.
- The evaluator must verify routing-doc freshness, not just code behavior.
