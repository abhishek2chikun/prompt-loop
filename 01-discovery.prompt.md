# Stage 1: Independent Repository Discovery

**Model/context:** fresh cost-efficient model with repository tools.

Build a solution-blind, evidence-based context package for a strong model in another conversation. Reuse verified project memory, inspect only relevant deltas, and create a new cycle without designing or implementing the feature.

## Input

- Neutral discovery brief: `<OBSERVED PROBLEM, USER WORKFLOW, OR REPOSITORY AREA>`
- Repository/path: `<OPTIONAL>`
- Existing workflow root, ticket, PR, or factual constraints: `<OPTIONAL>`

Do not receive the user's proposed library, architecture, schema, UI, algorithm, or implementation. If supplied material mixes outcome and solution, record the neutral outcome and exclude the proposed solution from discovery reasoning.

If no neutral scope exists, run `context-refresh`; do not infer an objective or open a feature cycle.

## Rules

1. Current code, tests, config, version control, and runtime behavior outrank memory and docs.
2. Read root and relevant nested project instructions before acting.
3. Preserve unrelated/uncommitted work; never reset or broadly reformat it.
4. Classify material claims as `confirmed`, `inferred`, `unknown`, or `contradicted`, with evidence.
5. Read project workflow memory before broad repository exploration, but verify inherited claims against current HEAD.
6. Read only prior cycles relevant by module, contract, lineage, risk, or deferred work.
7. Never reuse or reset a closed cycle for a new objective.
8. Do not choose product scope, architecture, or implementation.
9. Never expose secret values.
10. Durable artifacts, not chat history, are the handoff.
11. Every new/updated workflow artifact must declare `Workflow schema: five-stage-v1` and use current Stage 1-5 names. Do not emit legacy `Stage 0` or `00-discovery.md` instructions except when describing historical artifacts.

## Process

### 1. Classify The Run

Find the repository's workflow convention. Default:

```text
docs/ai-workflow/INDEX.md
docs/ai-workflow/PROJECT_CONTEXT.md
docs/ai-workflow/cycles/*/STATE.md
```

Classify:

- `first-cycle`: no usable history;
- `new-cycle`: distinct new problem/scope;
- `resume-cycle`: same unfinished objective;
- `follow-up-cycle`: extension/correction of a closed cycle;
- `divergent-cycle`: prior artifacts describe incompatible history; or
- `context-refresh`: no concrete neutral scope.

Similarity alone does not justify resume. A closed cycle always remains historical.

### 2. Load Relevant Project Memory

Read in order:

1. `INDEX.md`;
2. `PROJECT_CONTEXT.md`;
3. the current cycle for a true resume;
4. final reviews/return packets for accepted cycles touching the same area; and
5. older artifacts only when they source a current rule, risk, compatibility constraint, or deferred item.

For inherited claims record:

```markdown
| Claim/decision | Source/as-of SHA | Relevance | Freshness | Verification needed |
```

Freshness values:

- `carry-forward-confirmed`
- `carry-forward-provisional`
- `superseded`
- `contradicted`
- `historical-only`
- `unknown`

Check SHA ancestry, later cycles, relevant changed paths, branch divergence, contracts/manifests, and prior verdict. Never inherit planned, partial, rolled-back, or unverified behavior as implemented fact.

### 3. Establish Current Repository Truth

Record:

- repository root, branch, HEAD, remotes when relevant, and status;
- `git worktree list --porcelain`, including each active worktree path, branch, HEAD, and whether its HEAD is already an ancestor of the integration branch;
- languages, frameworks, build/package systems, and layout;
- current instructions and authoritative docs;
- relevant recent commits/diffs;
- public schemas, APIs, events, UI contracts, CLI surfaces, config, and deployment boundaries; and
- known doc drift.

When a trustworthy accepted SHA exists, inspect its relevant delta before rescanning unchanged areas.

Trace the scoped flow where possible:

```text
entry -> validation -> orchestration/domain -> persistence/dependency -> output/UI -> tests/observability
```

For each relevant component capture responsibility, paths/symbols, inputs/outputs, invariants, dependencies, errors/fallbacks, and what current tests prove.

Run only safe, cheap checks that reduce uncertainty. Defer destructive, paid, production, migration, or live-write operations as later evidence requirements.

Do not confuse branch ancestry with checkout state. A branch can be fully merged into the integration branch and still be actively checked out in another worktree. Never label a branch `not checked out`, `stale`, or safe to delete without checking the worktree list and repository policy.

### 4. Reconcile Memory, Docs, And Code

Look for:

- completion claims without implementation evidence;
- implemented behavior still documented as planned;
- stubs, TODOs, skipped tests, disabled paths, or mocked-only proof;
- legacy consumers and compatibility layers;
- generated files with another canonical source;
- missing runtime/environment assumptions;
- prior claims invalidated by later commits; and
- accepted behavior the new cycle must preserve.

State what exists, what is missing/broken, likely affected modules/contracts, factual unknowns, Stage 2 decisions, inherited capabilities, and evidence eventually required for success. Do not infer the desired solution.

### 5. Record The Planning Checkout And Future Worktree Policy

Stage 1 writes discovery artifacts in the repository's normal integration/default checkout. If launched elsewhere, locate the existing integration checkout or stop with its required path; do not create another checkout, feature branch, or worktree.

For a concrete Git cycle:

1. Detect repository branch/worktree policy and list existing worktrees.
2. Record the integration target, current checkout path, current HEAD, and cleanliness.
3. Record repository naming/location conventions for a future Stage 3 feature branch and worktree.
4. If the integration checkout contains unrelated changes, preserve and report them. Do not move them into a cycle branch.
5. Leave feature branch, feature worktree, and implementation baseline as `pending Stage 3 creation`.

Stage 2 will commit the discovery and planning artifacts on the integration branch. Stage 3 alone creates the implementation branch/worktree from that clean planning baseline. Stages 3-4 use the resulting canonical feature worktree; Stage 5 owns merge.

## Artifacts

Default layout:

```text
docs/ai-workflow/
  INDEX.md
  PROJECT_CONTEXT.md
  cycles/<YYYYMMDD-scope-slug>/
    STATE.md
    01-discovery.md
```

Register legacy cycle paths without moving or rewriting them.

### Project `INDEX.md`

```markdown
# AI Workflow Cycle Registry

Workflow schema: five-stage-v1
Project:
Workflow root:
Current repository HEAD:
Active cycle:
Last accepted/reviewed cycle:
Last updated:

## Cycle Registry
| Cycle ID | Type | Objective/scope | Status | Baseline SHA | Final reviewed feature SHA | Integration SHA/status | Areas/tags | Lineage | Artifact path |
## Active Cross-Cycle Blockers
## Deferred Opportunities
## Superseded Cycles/Decisions
```

Keep one row per cycle and exact artifact paths/SHAs. Detailed evidence stays inside cycle artifacts.

### Project `PROJECT_CONTEXT.md`

```markdown
# Verified Project Context

Workflow schema: five-stage-v1
Repository:
As-of HEAD:
Last reconciled by cycle:
Last updated:

## Product And Architecture Summary
## Current Capability Matrix
| Capability | Status | Evidence/source cycle | As-of SHA | Notes |
## Stable Contracts And Invariants
## Module/Ownership Map
## Canonical Build/Test/Run Commands
## Deployment/Environment State
## Active Risks, Blockers, And Verification Gaps
## Deferred Work
## Decision Ledger
| Decision | Current rule | Source cycle | As-of SHA | Supersedes |
## Known Documentation Drift
```

Promote only currently verified or accepted facts. Preserve historical reasoning in cycle artifacts.

### Cycle `01-discovery.md`

```markdown
# Discovery: <neutral scope>

Workflow schema: five-stage-v1
## Cycle Identity, Type, And Lineage
## Relevant Prior Cycles
| Cycle | Relationship/relevance | Verdict/SHA | Artifacts read |
## Prior-Knowledge Freshness
| Claim/decision | Source/SHA | Status | Evidence checked | Stage 2 consequence |
## Repository Delta Since Relevant Accepted Cycle
## Neutral Discovery Brief
## Proposed Solution Exposure Check
## Repository Identity And Instructions Read
## Current Product/Technical State
## Relevant Architecture And Data Flow
## Module/Symbol Map
| Area | Paths/symbols | Responsibility | Evidence |
## Contracts And Invariants
## Data, Persistence, And Dependencies
## Build/Test/Run Commands
| Purpose | Command | Verified now? | Result |
## Test Coverage And Blind Spots
## Documentation Drift And Contradictions
## Current Capability Status
## Inherited/Superseded Decisions
## Risks, Confirmed Facts, Inferences, And Unknowns
## Stage 2 Decision Questions
## Context Loading Map
### Must read
### Read on demand
### Cold evidence by path
## Evidence Eventually Required
```

### Cycle `STATE.md`

```markdown
# Workflow State

Workflow schema: five-stage-v1
Cycle ID/type/path:
Neutral discovery brief:
Objective: pending Stage 2 design | <existing objective for true resume>
Parent/relevant/superseded cycles:
Current stage/status: 1-discovery / complete | partial | blocked
Repository root:
Baseline/current HEAD:

Git/worktree contract:
- Integration target branch:
- Planning checkout absolute path:
- Planning checkout status:
- Stage 2 planning commit status: pending
- Proposed feature branch/name convention:
- Proposed worktree location:
- Feature branch: pending Stage 3 creation
- Worktree name/ID: pending Stage 3 creation
- Canonical feature worktree absolute path: pending Stage 3 creation
- Implementation baseline SHA: pending Stage 3 creation
- Feature worktree status: not-created
- Merge owner: Stage 5 persistent LLM
- Merge authorization: required | pre-authorized
- Merge status: not-started

Context topology:
- Persistent LLM lane: not-started
- Current owner: Stage 1 fresh SLM
- Next owner: Stage 2 persistent LLM
- Minimum next read set:
- Read on demand:
- Cold evidence:

Scope/non-goals/constraints:
Confirmed/inherited/reopened decisions:
Open decisions and assumptions:

Artifacts:
- Discovery:
- Design: pending
- Plan: pending
- Review anchor: pending
- Implementation log: pending
- Validation report: pending
- Return packet: pending
- Final review: pending

Evidence summary:
Known risks/blockers:
Execution ledger:
| Stage | Context | Start SHA | End SHA | Status | Artifacts |

Last completed stage: 1-discovery
Next required stage: 2-design-and-planning
Exact next action:
Last updated:
```

For `context-refresh`, update only project memory and optionally write `refreshes/<timestamp>-context-refresh.md`. Do not create/mutate a feature `STATE.md` or launch Stage 2.

Before ending any mode, rerun repository status after artifact writes. Distinguish pre-existing changes from files created by this workflow; do not report the worktree as clean when the newly written artifacts are untracked or modified.

## Completion Gate

Discovery is complete only when a fresh strong model can identify current behavior, evidence, contracts to preserve, relevant prior cycles/freshness, risks/unknowns, commands, planning-checkout identity, future worktree policy, and where to read next without broad rediscovery.

If anything material is missing, mark `partial` and leave the best exact handoff. Update the new cycle's active `INDEX.md` row, but do not mark it accepted.

## Final Response

```markdown
Stage 1 status: complete | partial | blocked | context-refreshed
Artifacts: <paths>
Cycle/lineage: <summary>
Planning checkout: <absolute path, integration branch, HEAD, status>
Future implementation worktree: pending Stage 3 creation; <recorded naming/location policy or blocker>
Confirmed current state: <3-7 bullets>
Inherited/superseded knowledge: <summary>
Critical unknowns: <bullets>
Next stage: Stage 2 | await-neutral-brief
Exact handoff: In the strong-model Plan Mode conversation, introduce the full idea and read <STATE> plus <01-discovery>. For context-refresh, provide a neutral scope and rerun Stage 1.
```
