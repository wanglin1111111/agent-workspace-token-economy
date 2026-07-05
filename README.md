# Agent Workspace Token Economy

> Interface shape determines Agent token cost. This skill provides frameworks for designing token-efficient Agent workspace interfaces using file-system-style namespace semantics.

## Why This Matters

Research shows that the same model, same data, and same task can consume **45% fewer tokens** and cost **39% less** purely based on how the workspace interface is designed. The interface is not just a technical detail — it's a primary cost lever.

## Key Concepts

- **Progressive Disclosure**: ls → grep → read → cite — the LLM-native workflow
- **Two-Layer Architecture**: Keep databases/storage at bottom; add namespace workspace on top
- **Token Cost Breakdown**: Bad interfaces inflate prompt tokens, reasoning tokens, AND output tokens
- **Artifact-Heavy Systems**: Experiment tracking, legal docs, R&D, multi-media — all benefit from namespace interfaces

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Core methodology and frameworks |
| `QUICKREF.md` | Fast lookup card with decision matrix |
| `TEST.md` | 5 test cases covering design, optimization, diagnosis, boundaries |

## Applicable Scenarios

- Building Agent systems with high token costs
- Designing workspace interfaces for AI Agents
- Optimizing multi-Agent artifact sharing
- Diagnosing Agent token inefficiency
- Choosing between relational and namespace interfaces

## License

MIT
