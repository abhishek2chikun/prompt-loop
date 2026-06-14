# Five-Stage LLM-to-SLM Delivery Loop

This repository contains five reusable prompts for moving a software change from repository discovery to reviewed integration:

1. independent discovery;
2. design and implementation planning;
3. implementation;
4. independent validation; and
5. final review and merge.

The workflow is designed for one strong reasoning model and multiple cost-efficient coding-model sessions. Its purpose is not merely lower token cost. It reduces repeated explanation, controls context growth, improves instruction following, keeps implementation isolated, and preserves enough evidence for a senior final review.

## Why This Exists

AI-assisted work across multiple projects usually breaks at handoffs:

- every new IDE or model session must rediscover the repository;
- product ideas bias repository investigation toward a preferred solution;
- smaller models receive vague plans and invent architecture or behavior;
- implementation and validation happen in another context, so the original planner loses the actual delta;
- completed workflow folders are reused for later features and historical state becomes ambiguous;
- agents edit or merge from the wrong checkout; and
- passing tests are mistaken for production readiness.

This loop turns chat context into durable, repository-grounded artifacts. Each stage has one owner, bounded inputs, required outputs, stop conditions, and an exact next action.

## Context Topology

```text
Fresh SLM A                  Persistent strong-model conversation P
Stage 1 discovery ---------> Stage 2 design + planning
                                  Plan Mode: grill and decide
                                  Plan-Writing Mode: commit plans on integration branch
                                                    |
                                                    | pause P
                                                    v
Fresh SLM B                  Fresh SLM C
Stage 3 create worktree
        + implementation --> Stage 4 independent validation
                                                    |
                                                    v
                              Resume conversation P
                              Stage 5 review + integration
```

Stage 2 and Stage 5 stay in the same strong-model conversation. It may be compacted, but `02-llm-review-anchor.md` preserves the decisions needed to resume. Stages 1, 3, and 4 use fresh contexts so discovery, implementation, and checking remain focused and independent.

## The Five Stages

| Stage | Model/context | Responsibility | Main artifacts |
|---|---|---|---|
| 1. Discovery | Fresh SLM | Build a solution-blind map of current repository reality and prior-cycle relevance; create no feature worktree | `01-discovery.md`, `STATE.md` |
| 2. Design + planning | Persistent strong LLM | Grill the idea, write SLM-executable plans, and commit workflow docs on the clean integration/default branch | `02-design.md`, `02-plan/`, `02-llm-review-anchor.md`, planning commit |
| 3. Implementation | Fresh SLM coordinator | Create the canonical feature worktree from the planning commit, then execute sequentially or through approved parallel lanes | `03-implementation-log.md`, feature commits |
| 4. Validation | Different fresh SLM | Independently inspect, debug, fix, test, and create a dense return packet | `04-validation-report.md`, `04-return-packet.md` |
| 5. Final review + merge | Resume Stage 2 LLM | Judge product/code/readiness, make bounded fixes, and integrate only after gates pass | `05-final-review.md`, merge/integration record |

## Why Discovery Is Independent

Stage 1 does not receive the proposed implementation. It receives only a neutral discovery brief:

- the observed problem;
- the affected user workflow; or
- the repository area to investigate.

It maps current behavior, contracts, tests, risks, extension points, and prior-cycle evidence without trying to prove the user's idea correct. The full idea is introduced in Stage 2, where the strong model compares it against independent repository truth.

If no neutral scope is supplied, Stage 1 performs a project-context refresh and does not open or reset a feature cycle.

## Why Design And Planning Are Combined

Design and planning use the same reasoning context and are now one prompt.

In IDE **Plan Mode**, Stage 2:

- asks one material question at a time using the IDE question UI;
- challenges the proposed solution;
- compares viable approaches;
- defines scope, contracts, failure behavior, and acceptance criteria; and
- obtains approval.

When the user clicks the IDE's **Implement/Agent** action, the same conversation enters **Stage 2 Plan-Writing Mode**. In this workflow, phrases such as "implement this plan," "build this plan," "proceed," or "go ahead" mean writing the detailed plan, not building the product. The conversation:

- writes `02-design.md`;
- writes repository-grounded task packets;
- writes `02-plan/implementation_guide.md` with the sequential/parallel execution shape;
- writes the compact review anchor; and
- commits the Stage 1/2 workflow artifacts on the integration/default branch and leaves that checkout clean.

Switching modes does not authorize production implementation. It materializes the approved plan so a fresh SLM can execute it.

Stage 2 enforces this mechanically:

- writes are allowlisted to workflow artifacts only;
- source, tests, migrations, configuration, dependencies, generated files, and `03-*` through `05-*` artifacts are forbidden;
- feature branch/worktree creation is forbidden;
- its internal execution checklist may contain only Stage 2 planning work, never implementation slices;
- `STATE.md` must remain `2-design-and-planning` with Stage 3 as the next owner; and
- a focused planning commit and clean integration/default checkout are required before handoff.

Production implementation starts only after the Stage 2 conversation stops and [03-implementation.prompt.md](03-implementation.prompt.md) is supplied in a fresh context.

## Git, Worktree, And Merge Ownership

Planning and implementation deliberately use different checkouts:

```markdown
Integration target branch:
Planning checkout absolute path:
Stage 2 planning commit/status:
Proposed feature branch/worktree:
Stage 3 creation policy:

Feature branch:
Worktree name/ID:
Canonical worktree absolute path:
Implementation baseline SHA:
Worktree status:
Merge owner: Stage 5 persistent LLM
Merge authorization: required | pre-authorized
Merge status: not-started
```

Ownership is deliberate:

- Stage 1 writes discovery artifacts in the normal integration/default checkout and records worktree policy, but creates no feature worktree.
- Stage 2 writes and commits discovery/design/plan artifacts there. It must finish with a clean checkout and no source changes.
- Stage 3 starts from that clean planning HEAD, creates the feature branch/worktree, records its actual identity, and performs all implementation there.
- Stage 4 validates and fixes the same feature worktree, records its absolute path and final SHA, and never merges.
- Stage 5 alone decides and performs integration after acceptance, authorization, clean-state checks, target-drift checks, and post-merge validation.

Because Stage 3 branches from the Stage 2 planning commit, the complete workflow context is already present in the feature worktree. There is no second live copy of the plan to reconcile.

A merge is not a deployment. `code-ready-release-unverified` remains unmerged by default. Production-release claims require the environment, distribution, migration, signing, rollout, and rollback evidence relevant to that project.

## Multi-Cycle Memory

The workflow is a repeating loop, not a folder that is reset for every idea.

```text
docs/ai-workflow/
  INDEX.md
  PROJECT_CONTEXT.md
  cycles/
    <YYYYMMDD>-<scope-slug>/
      STATE.md
      01-discovery.md
      02-design.md
      02-plan/
        00-plan-index.md
        implementation_guide.md
        <task files>.md
      02-llm-review-anchor.md
      03-implementation-log.md
      04-validation-report.md
      04-return-packet.md
      05-final-review.md
```

`INDEX.md` is the compact cycle registry. It records objectives, verdicts, baseline/final/integration SHAs, affected areas, lineage, blockers, and artifact paths.

`PROJECT_CONTEXT.md` is the verified current view: capabilities, contracts, module map, commands, deployment state, active risks, deferred work, and current decisions.

Each cycle directory is historical evidence for one objective. A closed cycle is never reset to Stage 1 for a new feature. New work gets a new linked cycle, even when it follows up or replaces an earlier feature.

## Prior-Knowledge Freshness

Stage 1 reads workflow memory before broad repository exploration, but does not trust it blindly. Prior claims are classified as:

- `carry-forward-confirmed`;
- `carry-forward-provisional`;
- `superseded`;
- `contradicted`;
- `historical-only`; or
- `unknown`.

The agent checks source SHA ancestry, later cycles, relevant path changes, branch divergence, and prior verdict. Planned, partial, rolled-back, or release-unverified behavior is never inherited as fully implemented fact.

This makes later cycles cheaper without allowing stale workflow files to override current code.

## Artifact Roles

### `STATE.md`

The authoritative control plane for one cycle. It records:

- cycle identity and lineage;
- immutable approved objective;
- current/next stage and context owner;
- repository baseline and current HEAD;
- canonical worktree contract;
- scope, decisions, assumptions, and blockers;
- artifact paths;
- acceptance/evidence summary;
- execution ledger;
- merge/integration status; and
- one exact next action.

### `02-llm-review-anchor.md`

A compact backup of the strong model's intent: approved decisions, rejected alternatives, invariants, risk-ranked acceptance criteria, expected change surface, review hypotheses, and rehydration order.

### `02-plan/implementation_guide.md`

The Stage 3 orchestration contract. It classifies the plan as `sequential`, `parallel-lanes`, or `hybrid`, then records the dependency graph, sequential spine, parallel lane ownership, integration order, shared validation gates, and fallback behavior.

Parallel execution is used only when substantial tasks have disjoint write ownership and fixed contracts. Parallel writers need isolated workspaces/patch return or separate child worktrees; concurrent edits to one checkout are forbidden. If safe isolation is unavailable, Stage 3 runs the same lanes sequentially and records why.

### `04-return-packet.md`

The implementation delta dossier returned to the original LLM. It contains:

- exact worktree, branch, baseline, and final validated SHA;
- commit ledger and changed symbols;
- contract/architecture deltas;
- acceptance coverage;
- Stage 4 fixes;
- command/runtime evidence;
- plan deviations and new repository facts;
- residual risks;
- integration preflight; and
- an ordered Stage 5 review map.

It references raw logs and artifacts by path rather than consuming the final review context with them.

## Running A Cycle

### 1. Run discovery

Start a fresh SLM context with [01-discovery.prompt.md](01-discovery.prompt.md). Provide the repository and a neutral problem/workflow/area. Do not include the preferred solution.

Expected result: a new cycle directory, independent discovery, project-memory updates, and a recorded planning checkout/future worktree policy. No feature branch/worktree is created.

### 2. Start the persistent LLM conversation

Open the strong model in Plan Mode with [02-design-and-planning.prompt.md](02-design-and-planning.prompt.md). Provide the full idea plus Stage 1 artifact paths.

Answer the focused questions. After approving the direction, click the IDE's Implement/Agent action. Under this workflow that action means: write the detailed Stage 2 design, task packets, implementation guide, review anchor, and boundary audit; commit those workflow docs on the integration/default branch; verify the checkout is clean; then stop.

### 3. Pause the LLM and implement

Keep the Stage 2 conversation available but paused. Start a fresh SLM from the clean integration/default checkout with [03-implementation.prompt.md](03-implementation.prompt.md). Stage 3 verifies the planning commit, creates the recorded feature branch/worktree from that HEAD, enters it, and only then may change source, tests, migrations, or configuration.

The Stage 3 coordinator follows `implementation_guide.md`. Sequential plans stay sequential. Parallel/hybrid plans use sub-agents for all ready safe lanes when isolation is available, then integrate lane outputs in the prescribed order. It commits coherent feature-branch changes and records evidence.

### 4. Validate independently

Start a different fresh SLM in the same canonical worktree with [04-validation.prompt.md](04-validation.prompt.md).

It distrusts implementation claims, inspects the actual diff, reproduces and fixes defects, runs risk-proportionate checks, and produces the return packet. It does not merge.

### 5. Resume the original LLM

Return to the Stage 2 conversation and run [05-review.prompt.md](05-review.prompt.md).

The minimum rehydration set is:

1. `STATE.md`;
2. `02-llm-review-anchor.md`;
3. `04-return-packet.md`;
4. the actual baseline-to-validated-head diff; and
5. high-risk files/evidence selected by the return packet.

Stage 5 reviews independently and merges only when its integration gate passes.

## Verdict And Integration Semantics

Stage 5 verdicts include:

- `accept`;
- `accept-with-followups`;
- `code-ready-release-unverified`;
- `fix-required`;
- returned to an earlier stage;
- `rollback`; or
- `blocked`.

Integration status is recorded separately:

- `not-started`;
- `awaiting-authorization`;
- `awaiting-pr-or-branch-protection`;
- `merge-conflict-returned`;
- `merged-post-merge-validation-failed`; or
- `merged-and-verified`.

This prevents an accepted design, green feature branch, local merge, CI pass, and production deployment from being collapsed into one misleading word: "done."

## Earliest-Responsible-Stage Routing

When a problem is found, return it to the earliest stage that can correct it:

- repository truth, lineage, or stale context -> Stage 1;
- outcome, scope, architecture, contracts, or task packet -> Stage 2;
- incomplete or incorrect implementation -> Stage 3;
- unresolved root cause or insufficient independent proof -> Stage 4;
- final judgment/integration policy -> Stage 5.

This improves the workflow itself instead of layering late patches over a faulty premise.

## Legacy Cycles

Older workflow runs may contain `00-discovery.md`, `01-design.md`, or separate Stage 1/2 prompts. Keep those directories unchanged as historical evidence. Register their existing paths in `INDEX.md` and let the new Stage 1 translate them into the current five-stage model when they are relevant.

Do not rename old artifacts merely for visual consistency. New cycles use the Stage 1-5 naming in this README.

## Definition Of Success

The workflow succeeds when:

- repository discovery is independent of the proposed solution;
- the strong model spends context on decisions and final judgment;
- SLMs receive bounded, executable instructions;
- every cycle has one unambiguous worktree and branch;
- maker/checker separation produces independent evidence;
- the final LLM can resume without broad rediscovery;
- merge occurs only after review, authorization, and post-merge proof;
- production readiness is reported honestly; and
- the next feature starts a linked cycle without corrupting prior history.
