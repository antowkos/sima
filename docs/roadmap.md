# SIMA Roadmap

> **Canonical development roadmap.** This is the single source of truth for SIMA priorities and milestone status. Technical specifications describe accepted design; files under `docs/plans/` are historical, bounded implementation plans and must not introduce competing roadmap priorities.

## Product direction

SIMA is a **verifiable project-knowledge and self-improvement harness**, not a clone of an agent's native auto-memory.

Its durable advantage is:

```text
normal agent work → bounded evidence → typed candidate → clean archivist
→ deterministic lifecycle gate → active memory/skill → sourced future brief
```

Native Claude Code/Codex memory may coexist, but it is not SIMA's source of truth. Durable project knowledge requested through SIMA must retain evidence, provenance, lifecycle status, and auditability.

## Current baseline

The current alpha already includes:

- project-local `.sima/` state with distinct personal, team, and system areas;
- active memory cards and reusable skills with explicit lifecycle states;
- task-scoped `sima brief`, with lexical, embedding, and hybrid retrieval;
- bounded worker runs, structured proposals, clean-session archivist review, deterministic apply gates, and personal auto-apply;
- explicit `sima remember` for user/review/agent knowledge requests;
- lifecycle-aware `sima memory list`, `sima skill list`, candidate inspection, cleanup, lint, and doctor;
- named Claude Code/Codex backend profiles;
- managed Claude/Codex project instructions, slash commands, and Codex skills;
- read-only team knowledge setup and sync: `team init`, `team pull`, `team status`.

## Priority 1: Knowledge dashboard and audit UX

**Goal:** Make SIMA's active knowledge understandable and inspectable by a human without weakening its evidence/lifecycle model.

### User-facing commands

```text
sima dashboard [--path .]
sima memory list [existing filters plus query/type/source]
sima memory show <id|path> [--path .]
sima memory activity [--path .]
```

### Dashboard content

- active personal memory and skills;
- active team memory and skills;
- pending/deferred/rejected candidate counts;
- recent apply/update/supersede/deprecate events;
- retrieval/index health and actionable warnings;
- short next-action suggestions.

### Memory inspection

- render cards as readable knowledge records rather than raw YAML;
- show type, trigger, summary, status, scope, timestamps, source proposal/run, and evidence links;
- expose lifecycle history when available;
- support search/filter by status, type, source/origin, and free-text query.

### Activity log

Record learning events separately from noisy worker logs:

```text
candidate_created → archivist_decided → knowledge_applied/updated/
superseded/deprecated → knowledge_recalled
```

Each event must point to the relevant card, proposal, brief, or evidence artifact. The log is for explanation and audit; it is not injected into agent context.

### Guardrails

- Do not make a Claude-style always-loaded global memory index.
- Do not let agents directly write active SIMA knowledge or bypass candidate/archivist/apply gates.
- Keep `sima brief` task-scoped; the dashboard must not become a prompt dump.
- Keep cards/proposals/evidence as the source of truth. Any concise index or dashboard cache is a derived projection.

### Done when

- a developer can answer “what does SIMA know, why, and what changed recently?” through CLI commands alone;
- `sima memory show` reaches the source proposal and evidence without manual filesystem navigation;
- stale/inactive knowledge is visibly distinguishable and excluded from briefs;
- dashboard output is compact and does not require model calls.

## Priority 2: Feedback and evaluation loop

**Goal:** Prove that SIMA improves future agent behavior rather than merely accumulating text. This is a core product direction, not a test-suite cleanup task.

### Feedback intake

Create a provenance-preserving intake path for:

- direct user corrections and accepted/rejected agent outcomes;
- bug reports and GitHub issues;
- PR review comments and follow-up fixes;
- verified positive examples worth protecting.

Feedback first becomes an auditable artifact. It may then propose one or more of: a memory/skill candidate, a regression-eval fixture, both, or neither. Raw feedback must never automatically become active knowledge.

### `sima eval`

Build an evaluation portfolio in increasing-cost layers:

1. **Deterministic/schema evals** — lifecycle, evidence, path safety, candidate validation, dedup, and apply gates.
2. **Golden retrieval evals** — task → expected relevant/irrelevant cards; report precision, recall, and unwanted-context rate.
3. **Learning-quality evals** — evidence-backed, triggerable, non-transient candidate and skill judgment.
4. **Trajectory/loop evals** — feedback or a verified run → candidate → archivist → apply/defer → later brief → expected future behavior.
5. **Model-backed behavioral evals** — bounded, versioned scenarios run against a named backend only when deterministic/golden layers are insufficient.

Core measurements:

- retrieval precision/recall and brief token budget;
- false promotion, missed promotion, duplicate, and stale-knowledge rates;
- future-task success/regression rate after a learned card or skill is applied;
- provenance coverage: every learned item and regression fixture points back to feedback/run evidence.

Failed real cases should become provenance-backed regression fixtures. Do not turn every run or every user comment into active memory.

## Priority 3: Shared knowledge promotion

**Goal:** Complete the intended team flow without allowing raw worker output directly into shared knowledge.

```text
personal learned card/skill
→ sima team propose
→ reviewable PR in team knowledge repository
→ merge
→ sima team pull
→ team-aware brief
```

Deliver `sima team propose <memory-or-skill-id|path>` with source provenance, evidence pointers, safety notes, and a clear PR payload. Team knowledge remains review-required and has precedence over conflicting personal knowledge during retrieval. Add conflict visibility to the dashboard before allowing a team card to shadow conflicting personal knowledge.

## Priority 4: Knowledge lifecycle and operator ergonomics

- controlled human revision that creates an auditable update proposal rather than silently mutating active cards;
- richer card/proposal history, including create/update/supersede/deprecate lineage;
- explicit stale-knowledge review and archival flows;
- handoff/resume artifacts for long-running work and fresh-session recovery;
- release/readiness checks and dogfood reports based on real teammate or project runs.

## Later: Ecosystem and adapters

- team policy and conflict-resolution UX;
- optional MCP/read-only dashboard adapter;
- platform-specific verification adapters (iOS/Xcode, backend/CI, web) that feed evidence into the common SIMA lifecycle;
- additional agent clients only after Claude Code and Codex flows remain reliable.

## Explicit non-goals

- replacing Claude Code/Codex native memory systems;
- a hidden unreviewed vector-store that silently changes agent behavior;
- storing transient run progress, raw logs, credentials, PR numbers, or issue state as active memory;
- a hosted dashboard before the local CLI audit flow is useful and dogfooded.
