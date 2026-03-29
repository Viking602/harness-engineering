# Sprint Contract Template

Use this template to define explicit agreements between generator and evaluator before implementation begins. Adapt fields as needed for your task.

---

## Contract Metadata

| Field | Value |
|-------|-------|
| **Contract ID** | `sprint-001` |
| **Created** | YYYY-MM-DD |
| **Expires** | YYYY-MM-DD or "upon completion" |
| **Generator** | Agent name/role |
| **Evaluator** | Agent name/role |
| **Parent Agent** | (if applicable) |

---

## Objective

One-sentence summary of what this sprint delivers.

> Example: Implement user authentication with JWT tokens, including login/logout endpoints and protected route middleware.

---

## Scope Boundaries

### In Scope
- Specific features, files, or capabilities to build
- Explicit APIs or interfaces to create

### Out of Scope (Deferred)
- What will NOT be built in this sprint
- Future work to schedule separately

### Constraints
- Technical constraints (e.g., "use existing database schema")
- Performance requirements (e.g., "login < 200ms")
- Compatibility requirements (e.g., "maintain backward compatibility")

---

## Deliverables

| # | Deliverable | Verification Method | Owner |
|---|-------------|---------------------|-------|
| 1 | Feature X implemented | Manual test + unit tests | Generator |
| 2 | API documentation updated | Doc review + example validation | Generator |
| 3 | Error handling for edge cases | Test cases pass | Generator |
| 4 | Routing docs updated if paths changed | Path exists check | Generator |

---

## Acceptance Criteria

Define hard thresholds. The evaluator will pass or fail based on these.

### Required (Must Pass)
- [ ] Criterion 1: Specific, testable condition
- [ ] Criterion 2: Specific, testable condition
- [ ] Criterion 3: Specific, testable condition

### Should Have (Warn if Missing)
- [ ] Criterion A
- [ ] Criterion B

### Verification Commands

```bash
# Commands the evaluator will run to verify
cd /path/to/project
npm test
npm run lint
./scripts/integration-test.sh
```

---

## Files May Touch

List specific files or directories the generator is allowed to modify:

- `src/auth/*.ts`
- `tests/auth/*.test.ts`
- `docs/api/auth.md`
- `AGENTS.md` (if new commands added)

Explicitly prohibited:
- `src/payment/*` (out of scope)
- Database migration files (handled separately)

---

## Stop Conditions

The generator MUST stop and escalate when:

1. **Technical blocker discovered** - implementation approach proves infeasible
2. **Scope expansion detected** - new requirements emerge mid-sprint
3. **Contract ambiguity** - acceptance criteria cannot be met as written
4. **External dependency failure** - required service/API unavailable
5. **Time budget exceeded** - effort exceeds estimated by >50%

---

## Evidence Requirements

Generator handoff must include:

- [ ] List of files changed
- [ ] Test results (pass/fail counts)
- [ ] Commands run and their output
- [ ] Known issues or risks
- [ ] Routing docs updated (if applicable)

Evaluator return must include:

- [ ] Pass/fail judgment for each required criterion
- [ ] Specific findings with evidence
- [ ] Screenshot, log excerpt, or command output for failures
- [ ] Recommended fixes for failed criteria

---

## Retry Budget

- **Maximum iterations**: 3
- **Escalation trigger**: Failed to pass after 3 iterations, or contract proves untestable

---

## Sign-off

| Role | Agent | Agreement |
|------|-------|-----------|
| Generator | | I can implement this scope against these criteria |
| Evaluator | | I can verify this scope against these criteria |
| Control Plane | | Priorities, budget, and stop conditions approved |

---

## Contract Evolution

If the contract must change mid-sprint:

1. Generator proposes amendment with rationale
2. Evaluator confirms new criteria are testable
3. Control plane approves scope/budget change
4. Update this document with amendment date and changes

---

## Example: Frontend Design Sprint

**Objective**: Create a responsive landing page for the product with custom typography

**Acceptance Criteria**:
- [ ] Design Quality: Visual elements form a coherent whole (not disparate parts)
- [ ] Originality: Evidence of custom decisions beyond template defaults
- [ ] Craft: Typography, spacing, and color harmony meet professional standards
- [ ] Functionality: All interactive elements work at all breakpoints

**Verification**:
- Evaluator uses Playwright to interact with live page at multiple viewport sizes
- Screenshots captured for each breakpoint
- Design quality scored against reference examples

**Stop Condition**: If page breaks on mobile viewport, stop and repair before continuing

---

## Example: Full-Stack Feature Sprint

**Objective**: Implement user profile API with avatar upload

**Acceptance Criteria**:
- [ ] GET /api/profile returns correct user data
- [ ] POST /api/profile/avatar accepts image ≤5MB
- [ ] Avatar is resized to 200x200 and 50x50 variants
- [ ] Database stores avatar URLs
- [ ] All endpoints return proper error codes

**Verification**:
```bash
curl -X GET http://localhost:3000/api/profile
npm test -- --grep "avatar"
```

**Stop Condition**: If image processing library fails to install, escalate to parent agent

---

*Based on Anthropic's "Harness design for long-running application development"*
