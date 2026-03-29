# Install For Codex

Use the skill entrypoint from:

- `harness-engineering/SKILL.md`
- `harness-engineering/agents/openai.yaml`

The source tree keeps the skill self-contained under `harness-engineering/`, so install the bundled `references/`, `agents/`, and `codex/agents/` directories together.

Install the Codex-specific agents from:

- `harness-engineering/codex/agents/harness-planner.toml`
- `harness-engineering/codex/agents/harness-dispatch-gate.toml`
- `harness-engineering/codex/agents/harness-generator.toml`
- `harness-engineering/codex/agents/harness-evaluator.toml`
- `harness-engineering/codex/agents/harness-doc-gardener.toml`

Target paths:

- `~/.codex/skills/harness-engineering/SKILL.md`
- `~/.codex/skills/harness-engineering/agents/openai.yaml`
- `~/.codex/skills/harness-engineering/references/`
- `~/.codex/skills/harness-engineering/codex/agents/`
- `~/.codex/agents/*.toml`

Example:

```bash
mkdir -p ~/.codex/skills/harness-engineering
install -D harness-engineering/SKILL.md ~/.codex/skills/harness-engineering/SKILL.md
install -D harness-engineering/agents/openai.yaml ~/.codex/skills/harness-engineering/agents/openai.yaml
cp -r harness-engineering/references ~/.codex/skills/harness-engineering/
mkdir -p ~/.codex/skills/harness-engineering/codex/agents
cp harness-engineering/codex/agents/*.toml ~/.codex/skills/harness-engineering/codex/agents/
cp harness-engineering/codex/agents/*.toml ~/.codex/agents/
```
