# Agent Workspace Token Economy — Quick Reference

## The 30-Second Version

**Interface shape = cost lever.** Same model, same data, same task → 45% fewer tokens, 39% less cost, just by using file-system-style namespace instead of raw relational interface.

## Progressive Disclosure Loop

```
discover (ls/stat) → search (grep/find) → read (cat/read) → cite (line numbers)
     ↑ low cost          ↑ scoped            ↑ on-demand       ↑ auditable
```

## Decision Matrix

| Task Type | Best Interface | Why |
|-----------|---------------|-----|
| Leaderboard / ranking | Relational | Single SELECT, fast |
| Cohort + log exploration | Namespace | Path handles, scoped grep |
| Evidence / audit trail | Namespace | Line numbers, stable paths |
| Multi-table aggregation | Relational | JOINs are native |
| Incident triage | Namespace | stderr → grep → line numbers |
| Schema-agnostic discovery | Namespace | ls works without schema |

## Architecture Pattern

```
KEEP: Database + Object Storage + APIs (bottom layer)
ADD: Workspace Namespace (top layer, for Agent)
  ├── Path addressing (/runs/{id}/stdout.log)
  ├── Directory listing (ls /runs/)
  ├── Scoped search (grep within /runs/{id}/)
  ├── On-demand read (read specific lines)
  ├── Push-down (filter+sort+limit in one call)
  └── Audit trail (line numbers, version refs)
```

## Token Cost Formula

```
total_cost = prompt_tokens + reasoning_tokens + output_tokens

Bad interface inflates ALL THREE:
  prompt_tokens    ↑ (more schema, more context to read)
  reasoning_tokens ↑ (more mental mapping, more correction)
  output_tokens    ↑ (more failed attempts, more retries)
```

## Design Checklist (Print This)

- [ ] Single-call discovery (ls or catalog, not full schema dump)
- [ ] Stable paths as handles (return to evidence without rebuilding context)
- [ ] Scoped search (directory-level grep, not global scan)
- [ ] Line-number references (built-in audit trail)
- [ ] Push-down operations (filter+sort+limit+project in one call)
- [ ] On-demand blob reading (never eager-load entire logs)
- [ ] Unified namespace across backends (DB + object storage + API)

## Red Flags (Anti-Patterns)

| Signal | Problem | Fix |
|--------|---------|-----|
| Agent reads schema >3 times | Schema too complex | Add namespace view |
| Tool chain >5 calls for simple Q | Interface indirection | Flatten with paths |
| Whole logs in context | Eager blob loading | Add grep-before-read |
| Cost varies 2× on same task | Interface instability | Standardize on namespace |
| Agent forgets original question | Attention drifting | Reduce context noise |

## Artifact-Heavy System Detector

Your system is **artifact-heavy** (and needs this skill) if it has:
- 100+ files/logs/reports per task
- Multiple storage backends (DB + object storage + cache)
- Agent spends more time finding data than analyzing it
- Cross-referencing between structured metrics and unstructured logs
- Version/lineage tracking requirements
