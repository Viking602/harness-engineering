# Harness Engineering Agents

Claude Code-compatible subagent definitions for harness-based workflows.

## Available Agents

| Agent | Purpose | Trigger |
|-------|---------|---------|
| `harness-planner` | Expand underspecified requests into durable specs, bounded chunks, risks, and acceptance checks | Scope needs clarification, work decomposition required |
| `harness-dispatch-gate` | Decide whether domain-specific implementation agent must own the next build step | Before code-writing phases, stack ownership decisions |
| `harness-generator` | Implement bounded chunks against explicit contracts | Contract execution, code generation |
| `harness-evaluator` | Independent judgment against contracts with evidence | Quality verification, compliance checking |
| `harness-doc-gardener` | Maintain routing docs after structure changes | Post-merge cleanup, index refreshing |

## Usage

These agents are designed to be dispatched by a parent agent using Claude Code's `Agent` tool.

### Basic Dispatch Pattern

```python
# Example: Dispatch planner
Agent(
    description="Plan the feature implementation",
    prompt="""
    Read the following context, then create a plan:
    - Contract: docs/contracts/feature-001.md
    - Current state: README.md

    Follow the harness-planner instructions and return the structured output.
    """,
    subagent_type="harness-planner"
)
```

### Role Chains

Common dispatch sequences:

1. **Plan → Implement**: `harness-planner` → `harness-generator`
2. **Implement → Verify**: `harness-generator` → `harness-evaluator`
3. **Full Loop**: `harness-planner` → `harness-dispatch-gate` → `harness-generator` → `harness-evaluator`
4. **Maintenance**: `harness-doc-gardener` (after structure changes)

### Return Value Handling

Each agent returns structured YAML that the parent must parse:

- **planner**: Parse `chunks`, `risks`, `dispatch_gate`
- **dispatch-gate**: Parse `status`, `required_implementation_owner`, `can_dispatch`
- **generator**: Parse `status`, `files_changed`, `verification`
- **evaluator**: Parse `status`, `findings`, `contract_compliance`
- **doc-gardener**: Parse `status`, `stale_references_fixed`

### Status Handling

| Status | Action Required |
|--------|-----------------|
| `DONE` / `PASS` / `UPDATED` | Integrate results, proceed to next step |
| `DONE_WITH_CONCERNS` | Review concerns, decide on acceptance |
| `FAIL` | Return to generator with specific fixes |
| `BLOCKED` | Escalate to parent or human |
| `NEEDS_CONTEXT` | Dispatch requested role or provide clarification |

## Differences from Codex

| Aspect | Codex | Claude Code |
|--------|-------|-------------|
| File format | `.toml` | `.md` with YAML frontmatter |
| Model config | `model = "gpt-5.4"` | `model: claude-opus-4-6` in frontmatter |
| Instructions | `developer_instructions` string | Markdown body |
| Reasoning | `model_reasoning_effort` | Use explicit thinking/reasoning in instructions |
| Nested dispatch | `codex exec`, `codex review` | Use `Agent` tool or return `NEEDS_CONTEXT` |

## Configuration

Add these agents to your Claude Code environment by:

1. Copying `.md` files to your project's `.claude/agents/` directory, OR
2. Referencing them from a skill's `agents/` directory

## Related Documentation

- `../SKILL.md` - Main harness-engineering skill
- `../references/long-running-harness-reference.md` - Full heuristics
- `../docs/subagent-dispatch-playbook.md` - Dispatch patterns (Codex-specific but conceptually similar)
