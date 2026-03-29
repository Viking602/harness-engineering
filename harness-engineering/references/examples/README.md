# Harness Examples

Practical templates and checklists for implementing harness patterns.

## Available Examples

### Sprint Contract Template
`./sprint-contract-template.md`

A complete sprint contract template with:
- Contract metadata and sign-off fields
- Scope boundaries (in/out of scope)
- Acceptance criteria with hard thresholds
- Stop conditions and escalation triggers
- Evidence requirements for generator and evaluator
- Real examples for frontend design and full-stack feature sprints

Use this when establishing explicit agreements between generator and evaluator roles.

### Evaluator Checklist
`./evaluator-checklist.md`

A comprehensive evaluation checklist with:
- Pre-evaluation setup steps
- Universal criteria (correctness, completeness, quality)
- Task-specific checklists for:
  - Code implementation
  - Frontend design
  - API/Backend
  - Documentation
- External verification methods (Playwright, API testing, logs)
- Scoring guide with strict evaluation patterns
- Evidence collection templates
- Common failure patterns to watch for

Use this when evaluating agent outputs to ensure consistent, evidence-based judgment.

## How To Use These Examples

1. **Copy and adapt** - These are templates, not rigid forms. Remove sections that don't apply to your task.

2. **Start with contracts** - For multi-role harnesses, begin with the sprint contract template to establish shared understanding.

3. **Evaluators verify against checklists** - Use the evaluator checklist during review to ensure consistent coverage.

4. **Iterate the templates** - If you find yourself adding the same custom sections repeatedly, consider updating the template.

## Related References

- `../long-running-harness-reference.md` - Full article-backed heuristics
- `../../SKILL.md` - Main skill entrypoint with simplification guidance
- `../../../docs/runtime-orchestration-principles.md` - Runtime orchestration principles
