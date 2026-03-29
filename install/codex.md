# Install For Codex

Use the skill entrypoint from:

- `harness-engineering/SKILL.md`
- `harness-engineering/agents/openai.yaml`

The source tree keeps the skill in `harness-engineering/`, but Codex expects the installed entrypoint at `~/.codex/skills/harness-engineering/SKILL.md`. Rewrite the relative `docs/` and `codex/agents/` paths during installation so the installed copy still resolves correctly.

Install the Codex-specific agents from:

- `codex/agents/harness-planner.toml`
- `codex/agents/harness-dispatch-gate.toml`
- `codex/agents/harness-generator.toml`
- `codex/agents/harness-evaluator.toml`
- `codex/agents/harness-doc-gardener.toml`

Target paths:

- `~/.codex/skills/harness-engineering/SKILL.md`
- `~/.codex/skills/harness-engineering/agents/openai.yaml`
- `~/.codex/skills/harness-engineering/references/`
- `~/.codex/skills/harness-engineering/docs/`
- `~/.codex/skills/harness-engineering/codex/agents/`
- `~/.codex/agents/*.toml`

Example:

```bash
mkdir -p ~/.codex/skills/harness-engineering
sed -e 's#`../docs/#`docs/#g' -e 's#`../codex/agents/#`codex/agents/#g' harness-engineering/SKILL.md > ~/.codex/skills/harness-engineering/SKILL.md
install -D harness-engineering/agents/openai.yaml ~/.codex/skills/harness-engineering/agents/openai.yaml
install -D harness-engineering/references/long-running-harness-reference.md ~/.codex/skills/harness-engineering/references/long-running-harness-reference.md
install -D docs/harness-principles.md ~/.codex/skills/harness-engineering/docs/harness-principles.md
install -D docs/runtime-orchestration-principles.md ~/.codex/skills/harness-engineering/docs/runtime-orchestration-principles.md
install -D docs/subagent-dispatch-playbook.md ~/.codex/skills/harness-engineering/docs/subagent-dispatch-playbook.md
install -D docs/doc-index-maintenance.md ~/.codex/skills/harness-engineering/docs/doc-index-maintenance.md
install -D docs/openai-docs-index-pattern.md ~/.codex/skills/harness-engineering/docs/openai-docs-index-pattern.md
install -D codex/agents/harness-planner.toml ~/.codex/skills/harness-engineering/codex/agents/harness-planner.toml
install -D codex/agents/harness-dispatch-gate.toml ~/.codex/skills/harness-engineering/codex/agents/harness-dispatch-gate.toml
install -D codex/agents/harness-generator.toml ~/.codex/skills/harness-engineering/codex/agents/harness-generator.toml
install -D codex/agents/harness-evaluator.toml ~/.codex/skills/harness-engineering/codex/agents/harness-evaluator.toml
install -D codex/agents/harness-doc-gardener.toml ~/.codex/skills/harness-engineering/codex/agents/harness-doc-gardener.toml
install -D codex/agents/harness-planner.toml ~/.codex/agents/harness-planner.toml
install -D codex/agents/harness-dispatch-gate.toml ~/.codex/agents/harness-dispatch-gate.toml
install -D codex/agents/harness-generator.toml ~/.codex/agents/harness-generator.toml
install -D codex/agents/harness-evaluator.toml ~/.codex/agents/harness-evaluator.toml
install -D codex/agents/harness-doc-gardener.toml ~/.codex/agents/harness-doc-gardener.toml
```
