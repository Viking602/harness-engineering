# Subagent Migration: Codex to Claude Code

This guide explains how to adapt the harness subagents between Codex and Claude Code environments.

## File Format Comparison

### Codex (.toml)

```toml
name = "harness-planner"
description = "Use when a long-running coding task needs a planning pass..."
model = "gpt-5.4"
model_reasoning_effort = "xhigh"
sandbox_mode = "read-only"
developer_instructions = """
Own planning work for long-running coding tasks...

Working mode:
1. Read the parent-provided artifacts...
2. ...

Return:
- objective and scope
- assumptions and hard constraints
...
"""
```

### Claude Code (.md)

```markdown
---
name: harness-planner
description: |
  Use when a long-running coding task needs a planning pass...
model: claude-opus-4-6
sandbox_mode: read-only
---

# Harness Planner

Own planning work for long-running coding tasks...

## Working Mode

1. Read the parent-provided artifacts...
2. ...

## Return Format

```yaml
objective: "..."
scope: "..."
```
```

## Key Differences

| Aspect | Codex | Claude Code |
|--------|-------|-------------|
| **File extension** | `.toml` | `.md` |
| **Format** | INI-style key-value | YAML frontmatter + Markdown |
| **Model config** | `model = "gpt-5.4"` | `model: claude-opus-4-6` |
| **Reasoning control** | `model_reasoning_effort = "xhigh"` | Use explicit instructions |
| **Instructions** | `developer_instructions` string | Markdown body |
| **Line breaks** | Triple-quoted strings | Standard Markdown |
| **Code blocks** | Escaped or raw | Standard fenced blocks |
| **Nested dispatch** | `codex exec`, `codex review` | `Agent` tool or return status |

## Agent Mapping

Both platforms support the same five core agents:

| Agent | Codex File | Claude Code File |
|-------|------------|------------------|
| Planner | `harness-planner.toml` | `harness-planner.md` |
| Dispatch Gate | `harness-dispatch-gate.toml` | `harness-dispatch-gate.md` |
| Generator | `harness-generator.toml` | `harness-generator.md` |
| Evaluator | `harness-evaluator.toml` | `harness-evaluator.md` |
| Doc Gardener | `harness-doc-gardener.toml` | `harness-doc-gardener.md` |

## Instruction Adaptations

### 1. Nested Dispatch Handling

**Codex:**
```toml
developer_instructions = """
Do not use `codex exec`, `codex review`, or other nested Codex CLI delegation paths.
If another role is needed, return `BLOCKED` or `NEEDS_CONTEXT`.
"""
```

**Claude Code:**
```markdown
## Rules

- Do not open nested agent sessions for delegation or review
- Do not use the `Agent` tool to spawn subagents from within this agent
- If another role is needed, return `BLOCKED` or `NEEDS_CONTEXT` in your output
```

### 2. Return Format Specification

**Codex:**
```toml
developer_instructions = """
Return:
- status: PASS | FAIL | BLOCKED
- findings first
- supporting evidence
...
"""
```

**Claude Code:**
```markdown
## Return Format

```yaml
status: PASS | FAIL | BLOCKED
findings:
  critical: []
  warnings: []
evidence: []
```
```

### 3. Structured Output Requirements

Claude Code benefits from more explicit structured output definitions since there's no `model_reasoning_effort` control.

Add explicit sections:
- `## Return Format` with YAML examples
- `## Status Definitions` explaining each status
- `## Rules` for behavioral constraints

## Using the Agents

### Codex

```bash
# Codex uses built-in agent dispatch
codex exec --agent harness-planner --prompt "Plan the feature"
```

### Claude Code

```python
# Claude Code uses the Agent tool
Agent(
    description="Plan feature implementation",
    prompt="Read the contract at docs/contract.md and create a plan...",
    subagent_type="harness-planner"
)
```

## Directory Structure

### Codex
```
codex/
└── agents/
    ├── harness-planner.toml
    ├── harness-dispatch-gate.toml
    ├── harness-generator.toml
    ├── harness-evaluator.toml
    └── harness-doc-gardener.toml
```

### Claude Code
```
harness-engineering/
└── agents/
    ├── README.md
    ├── harness-planner.md
    ├── harness-dispatch-gate.md
    ├── harness-generator.md
    ├── harness-evaluator.md
    ├── harness-doc-gardener.md
    └── openai.yaml  # UI metadata
```

## Installation

### Codex

Copy `.toml` files to `~/.codex/agents/`:

```bash
cp harness-engineering/codex/agents/*.toml ~/.codex/agents/
```

### Claude Code

Option 1: Copy to project directory:
```bash
cp -r harness-engineering/agents/*.md /path/to/project/.claude/agents/
```

Option 2: Reference from skill (already done in this repo):
```yaml
# In your skill's SKILL.md
# Agents are automatically available when skill is loaded
```

## Maintaining Parity

When updating agents, update both formats:

1. Make changes to `.toml` files for Codex
2. Port equivalent changes to `.md` files for Claude Code
3. Ensure return formats remain compatible
4. Verify status codes match between platforms

## Testing

### Codex
```bash
codex exec --agent harness-planner --prompt "Test prompt"
```

### Claude Code
```python
# In a test script or interactive session
Agent(
    description="Test harness-planner",
    prompt="Test prompt",
    subagent_type="harness-planner"
)
```

## Common Issues

### Issue: Different Model Capabilities

Codex uses GPT-5.4, Claude Code uses Claude Opus 4.6.

**Solution:** Both support long contexts and reasoning. Adjust expectations but use the same agent patterns.

### Issue: Sandbox Modes

Both support:
- `read-only`: Agent cannot write files
- `workspace-write`: Agent can modify workspace

**No changes needed** - use the same sandbox_mode values.

### Issue: Dispatch Mechanism

Codex has built-in agent dispatch. Claude Code requires parent agent to use `Agent` tool.

**Solution:**
- Codex agents can reference `codex exec` in instructions
- Claude Code agents should reference returning `BLOCKED`/`NEEDS_CONTEXT` for parent dispatch

## Future Considerations

As both platforms evolve:

1. **Monitor for convergence** - agent formats may standardize
2. **Keep abstractions clean** - business logic should be platform-agnostic
3. **Test on both platforms** when making changes
4. **Document platform-specific quirks** in this guide
