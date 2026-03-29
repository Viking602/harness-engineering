# Harness Engineering

This is the standalone `harness-engineering` project.

## Layout

- `harness-engineering/SKILL.md`: skill entrypoint
- `harness-engineering/agents/openai.yaml`: Codex UI metadata
- `harness-engineering/references/`: bundled skill references loaded on demand
- `docs/`: project docs, principles, and dispatch references
- `codex/agents/`: Codex-specific custom agents
- `install/`: installation guides

## Intent

Keep the skill entrypoint small. Put the larger method docs, install notes, and Codex-only runtime assets here.
The source tree keeps the skill under `harness-engineering/`. Installation flattens that entrypoint to `~/.codex/skills/harness-engineering/SKILL.md`.
