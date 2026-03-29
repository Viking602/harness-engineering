# Evaluator Checklist

Use this checklist when evaluating agent outputs. Adapt sections based on the task type (code, design, docs, etc.).

---

## Pre-Evaluation Setup

### 1. Access the Real Artifact

Do not evaluate based on the generator's description alone. Access the actual output:

- **Code**: Check out the branch, run the code, run tests
- **UI/Design**: Open in browser, interact with live page (use Playwright if available)
- **APIs**: Make actual requests, verify responses
- **Docs**: Read rendered docs, verify links work

### 2. Load the Contract

- Read the sprint contract or acceptance criteria
- Confirm you understand each criterion
- Note any ambiguous criteria to clarify

---

## Universal Evaluation Criteria

### Correctness

- [ ] Does it work? (Not "should work" or "looks correct")
- [ ] Are there failing tests or errors in logs?
- [ ] Does it handle edge cases?
- [ ] Are there obvious bugs on basic usage?

### Completeness

- [ ] Are all contract items addressed?
- [ ] Are there stubbed or TODO sections that should be complete?
- [ ] Is error handling implemented?
- [ ] Is documentation updated?

### Quality

- [ ] Is the code readable and maintainable?
- [ ] Are there obvious performance issues?
- [ ] Are there security concerns?
- [ ] Does it follow project conventions?

---

## Task-Specific Checklists

### Code Implementation

#### Functionality
- [ ] Main use case works end-to-end
- [ ] Error paths handled gracefully
- [ ] Edge cases covered (empty input, max size, etc.)
- [ ] Integration points work (APIs, databases, etc.)

#### Verification Commands
```bash
# Run these and verify output
npm test                    # or pytest, go test, etc.
npm run lint               # or equivalent
npm run build              # verify builds without errors
npm run typecheck          # if TypeScript
```

#### Code Quality
- [ ] No obvious code smells (duplication, long functions, etc.)
- [ ] Appropriate abstractions (not over-engineered, not under-engineered)
- [ ] Error messages are helpful
- [ ] Logging is appropriate (not excessive, not silent)

### Frontend Design

#### Design Quality
- [ ] Visual coherence: Elements feel like they belong together
- [ ] Typography: Readable, appropriate hierarchy, consistent
- [ ] Spacing: Comfortable, consistent rhythm
- [ ] Color: Harmonious, appropriate contrast, accessible

#### Originality
- [ ] Evidence of custom decisions (not just defaults)
- [ ] Layout shows intention (not generic template)
- [ ] Micro-interactions considered

#### Craft
- [ ] Alignment is precise
- [ ] Responsive behavior is intentional
- [ ] Animation is smooth and purposeful
- [ ] No obvious visual bugs

#### Functionality
- [ ] Works at all specified breakpoints
- [ ] Interactive elements respond correctly
- [ ] Forms validate and submit
- [ ] Navigation works

#### Verification
```bash
# Use Playwright or similar
npx playwright test --headed  # visually inspect
npx playwright screenshot     # capture for comparison
```

### API/Backend

#### Contract Compliance
- [ ] Endpoints match spec
- [ ] Request/response formats correct
- [ ] Status codes appropriate
- [ ] Error responses follow standard format

#### Verification
```bash
# Test actual endpoints
curl -X GET http://localhost:3000/api/resource
curl -X POST http://localhost:3000/api/resource -d '{...}'

# Check database state if applicable
psql -c "SELECT * FROM resources WHERE ..."
```

#### Performance
- [ ] Response times acceptable
- [ ] No N+1 query problems
- [ ] Appropriate caching

### Documentation

#### Accuracy
- [ ] Instructions work when followed literally
- [ ] Code examples run without modification
- [ ] Links resolve correctly
- [ ] No stale references

#### Completeness
- [ ] Required setup steps included
- [ ] Configuration options documented
- [ ] Troubleshooting section covers common issues

#### Clarity
- [ ] Target audience appropriate
- [ ] Structure is navigable
- [ ] Examples are concrete

---

## External Verification Methods

### Browser Automation (Playwright)

Use for UI evaluation:

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page()
    page.goto('http://localhost:3000')

    # Interact like a user
    page.click('button[data-testid="submit"]')

    # Verify state
    assert page.inner_text('.success-message') == 'Saved!'

    # Screenshot for evidence
    page.screenshot(path='result.png')
    browser.close()
```

### API Testing

```bash
# Capture full response for evaluation
curl -s -w "\nHTTP_CODE: %{http_code}\n" http://localhost:3000/api/test | tee response.txt

# Check specific fields
jq '.status' response.txt
```

### Log Analysis

```bash
# Check for errors
tail -100 logs/app.log | grep -i error

# Check performance
tail -100 logs/app.log | grep "response_time"
```

### Database Verification

```bash
# Verify data was written correctly
psql -d mydb -c "SELECT id, status FROM users WHERE created_at > NOW() - INTERVAL '1 hour';"
```

---

## Scoring Guide

### Strict Evaluation (Anthropic Pattern)

Tune yourself to be skeptical. Better to flag issues that aren't real than miss real issues.

| Score | Meaning | Action |
|-------|---------|--------|
| **Pass** | Meets all required criteria | Approve, note any "should have" gaps |
| **Conditional Pass** | Required criteria met, significant "should have" gaps | Approve with warnings |
| **Fail** | Any required criterion not met | Reject with specific fixes required |
| **Block** | Cannot evaluate (system down, contract unclear) | Escalate to parent agent |

### Grading Rubric

For subjective criteria (design quality, code elegance), use explicit rubrics:

**Design Quality Example:**
- **Excellent (4)**: Cohesive, polished, shows clear intention
- **Good (3)**: Coherent, professional, minor polish issues
- **Acceptable (2)**: Functional, some inconsistencies
- **Poor (1)**: Incoherent, obvious template usage
- **Fail (0)**: Broken, unusable, or incomplete

Required threshold: ≥3

---

## Evidence Collection

Every evaluation must include evidence, not just judgment:

### Required Evidence

- **Pass**: Command output showing success, screenshot showing correct state
- **Fail**: Exact error message, screenshot showing problem, steps to reproduce
- **Measurement**: Timings, counts, or metrics with values

### Evidence Template

```markdown
## Evaluation: [Criterion Name]

**Judgment**: PASS / FAIL

**Evidence**:
```
[Command output or screenshot description]
```

**Finding**:
Specific description of what was observed.

**Recommended Fix** (if FAIL):
Concrete steps to address the issue.
```

---

## Common Failure Patterns

Watch for these recurring issues:

1. **Works on my machine**: Generator tested in different environment
2. **Happy path only**: No error handling verified
3. **Stubs left in**: TODO comments, placeholder data
4. **Routing doc drift**: AGENTS.md or docs/index.md stale
5. **Test tunnel vision**: Tests pass but actual usage fails
6. **Performance blind spot**: Works for 1 item, fails for 1000
7. **Security oversight**: Obvious injection vulnerabilities

---

## Post-Evaluation

### Pass

- Confirm all required criteria passed
- Note any "should have" gaps for future sprints
- Verify routing docs are fresh if surface changed
- Return with evidence summary

### Fail

- List each failed criterion with specific evidence
- Prioritize fixes (blockers vs nice-to-have)
- Suggest which contract items to relax if deadline pressures
- Return with actionable fix list

### Block

- Explain why evaluation cannot proceed
- State what is needed to unblock (system access, contract clarification, etc.)
- Escalate to parent agent or human

---

## Iteration Tracking

Track findings across iterations to identify patterns:

| Iteration | Findings | Fixed | New Issues |
|-----------|----------|-------|------------|
| 1 | 5 | 5 | 2 |
| 2 | 2 | 2 | 0 |
| 3 | 0 | - | - |

If issues keep recurring, suggest:
- Updating contract template
- Adding automated check
- Updating project conventions doc

---

*Based on Anthropic's "Harness design for long-running application development"*
