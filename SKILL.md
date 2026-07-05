---
name: agent-workspace-token-economy
description: Design Agent workspace interfaces for token efficiency. Use when building artifact-heavy Agent systems, optimizing Agent token costs, or choosing between relational vs filesystem-style interfaces for AI Agents.
---

# Agent Workspace Token Economy

## Overview

Interface shape determines Agent token cost. The same model, same data, same task can consume 45% fewer tokens and cost 39% less purely based on how the workspace is presented to the Agent. This skill provides frameworks for designing token-efficient Agent interfaces.

## Core Thesis

**Token efficiency is not just about model selection and prompt engineering.** The operation interface (how data, tools, and artifacts are exposed to the Agent) is a primary cost lever. File-system-style namespace interfaces consistently outperform raw relational interfaces for exploratory and composite tasks.

## The Progressive Disclosure Pattern

LLMs are extensively trained on shell, Unix tools, Git, and code navigation corpora. The most stable behavioral pattern:

```
ls → grep → read → cite line numbers
```

This is **progressive disclosure**: low-cost discovery first, targeted reading second, scoped search third, evidence citation last.

### Four Pillars of File-System Semantics

| Pillar | Mechanism | Why It Matters |
|--------|-----------|----------------|
| Path as Handle | `/runs/abc/stdout.log` is both name and address | Agent can return to evidence without rebuilding context |
| Scoped Search | Global grep for discovery, directory grep for extraction | Reduces irrelevant context injection |
| Line Numbers | Natural audit trail | Built-in evidence referencing for verification |
| Push-Down | Filter, sort, limit, project in one call | Fewer conversation turns = less context re-injection |

## Two-Layer Architecture

```
┌─────────────────────────────────────────┐
│  Agent Workspace Layer (Namespace)      │
│  Path addressing, directory listing,    │
│  on-demand read, scoped search,         │
│  permission boundaries, audit trails    │
├─────────────────────────────────────────┤
│  Storage Layer (unchanged)              │
│  Database (reliability, transactions)   │
│  Object storage (large files, blobs)    │
│  APIs (permissions, domain logic)       │
└─────────────────────────────────────────┘
```

**Key insight**: Don't replace databases with files. Add a workspace namespace layer ON TOP of existing storage. The bottom layer keeps doing what it's good at; the top layer gives the Agent an interface it navigates efficiently.

## Interface Comparison Framework

| Dimension | Relational Interface | Namespace Interface | Winner |
|-----------|---------------------|---------------------|--------|
| Single-shot structured query | Schema → SELECT → done | Catalog → find → done | Relational |
| Composite exploration | Multiple JOINs, blob refs, context bloat | ls → grep → read with line numbers | Namespace |
| Log/evidence retrieval | grep_blob on indirect refs, whole blobs into context | Recursive grep with scope, line-number citations | Namespace |
| Schema discovery cost | Agent reads schema, guesses JOIN paths | Agent lists directories, stats objects | Namespace |
| Error recovery | Wrong query = full cost still charged | Wrong path = cheap retry with new ls | Namespace |

**Decision rule**: For clear aggregations and leaderboards, relational wins. For "find cohort → check logs → cite evidence" composite tasks, namespace wins by 2-3×.

## Token Cost Breakdown

Agent token consumption has three layers:

1. **Prompt tokens** — everything the Agent reads (schema, query results, logs, tool descriptions)
2. **Reasoning tokens** — internal thinking ("what do I look at next?", "how do these tables relate?", "did I miss something?")
3. **Output tokens** — final answer and tool calls

Bad interfaces inflate all three layers:
- More schema to read → more prompt tokens
- More mental mapping to connect entities → more reasoning tokens  
- More failed attempts → more rounds → compounding prompt + reasoning tokens

## When to Apply This Skill

### High-Impact Scenarios

- **Experiment tracking systems**: runs, metrics, checkpoints, logs, git states
- **Legal document Agent**: contracts, precedents, exhibits, annotations, versions
- **Data analysis Agent**: notebooks, CSVs, charts, reports, database results
- **R&D Agent**: issues, PRs, CI logs, build artifacts, deployment records
- **Multi-media Agent**: originals, thumbnails, transcriptions, timestamps, embeddings
- **Multi-Agent collaboration**: shared artifacts, version locks, update monitoring

### Diagnostic Signals

Your Agent system needs this skill if:
- Agent spends most turns discovering schema before answering
- Tool call chains exceed 5 steps for simple questions
- Logs/blobs are regularly pulled entirely into context
- Same question costs 2× more on different interfaces
- Agent gets "lost" mid-task, forgetting the original question

## Design Checklist

- [ ] Can the Agent discover available data with a single low-cost call?
- [ ] Are paths/addresses stable across sessions?
- [ ] Can search be scoped to a subset (directory, cohort)?
- [ ] Do results include line numbers or position references?
- [ ] Can filtering, sorting, and projection be pushed down in one call?
- [ ] Does the Agent need to understand schema before starting exploration?
- [ ] Are blob/log reads on-demand, not eager?
- [ ] Is there a unified namespace across different storage backends?

## Measuring Token Efficiency

```yaml
metrics:
  prompt_tokens_per_task: total input tokens / task count
  reasoning_tokens_per_task: internal thinking tokens / task count
  cost_per_correct_answer: total_cost / correct_answers
  turns_per_task: tool_call_rounds / task count
  error_rate: failed_attempts / total_attempts
  
benchmarks:
  - same_model: fixed model across all tests
  - same_data: identical dataset
  - same_tasks: identical question set
  - stateless: fresh context per run
  - repeated: ≥10 runs per condition for stability
```

## Anti-Patterns

1. **Schema-first exploration**: Forcing Agent to read full schema before any action
2. **Blob eager loading**: Pulling entire logs into context when only 3 lines matter
3. **Flat tool namespace**: Hundreds of tools in a single list, no hierarchy
4. **ID indirection**: Agent must resolve ID → ref → blob → content chains
5. **Stateful context dependency**: Requiring prior conversation to understand current task
