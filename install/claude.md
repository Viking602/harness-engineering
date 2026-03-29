# Install For Claude Code

Use the skill entrypoint from:

- `harness-engineering/SKILL.md`
- `harness-engineering/agents/openai.yaml`

The source tree keeps the skill self-contained under `harness-engineering/`, so install the bundled `references/` and `agents/` directories together.

Install the Claude Code-specific agents from:

- `harness-engineering/agents/harness-planner.md`
- `harness-engineering/agents/harness-dispatch-gate.md`
- `harness-engineering/agents/harness-generator.md`
- `harness-engineering/agents/harness-evaluator.md`
- `harness-engineering/agents/harness-doc-gardener.md`

Target paths:

- `~/.claude/skills/harness-engineering/SKILL.md`
- `~/.claude/skills/harness-engineering/agents/openai.yaml`
- `~/.claude/skills/harness-engineering/references/`
- `~/.claude/skills/harness-engineering/agents/*.md`
- `~/.claude/agents/*.md` (symlink or copy for global agent access)

Example:

```bash
mkdir -p ~/.claude/skills/harness-engineering

# Install skill entrypoint
install -D harness-engineering/SKILL.md ~/.claude/skills/harness-engineering/SKILL.md

# Install agent registry
install -D harness-engineering/agents/openai.yaml ~/.claude/skills/harness-engineering/agents/openai.yaml

# Install reference docs
cp -r harness-engineering/references ~/.claude/skills/harness-engineering/

# Install all agents to skill directory
cp harness-engineering/agents/*.md ~/.claude/skills/harness-engineering/agents/

# Optional: Install agents to global agents directory for direct access
mkdir -p ~/.claude/agents
cp harness-engineering/agents/harness-*.md ~/.claude/agents/
```

## Platform Differences

| Aspect | Codex | Claude Code |
|--------|-------|-------------|
| Skill path | `~/.codex/skills/` | `~/.claude/skills/` |
| Agent path | `~/.codex/agents/` | `~/.claude/agents/` |
| Agent format | `.toml` | `.md` with YAML frontmatter |
| Model | `gpt-5.4` | `claude-opus-4-6` |

## Verification

After installation, verify the skill is recognized:

```bash
# Claude Code will automatically load skills from ~/.claude/skills/
# Agents should be available via the Agent tool with subagent_type parameter
```

## Project-Local Installation

Alternatively, install to your project directory:

```bash
mkdir -p .claude/skills/harness-engineering
mkdir -p .claude/agents

# Copy skill files
cp -r harness-engineering/* .claude/skills/harness-engineering/

# Copy agents for project-local access
cp harness-engineering/agents/harness-*.md .claude/agents/
```
