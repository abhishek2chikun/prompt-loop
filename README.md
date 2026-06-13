# Six-Stage LLM-to-SLM Product Delivery Loop

This repository contains a reusable workflow for designing, building, validating, and shipping software with a combination of:

- a strong reasoning model for product thinking, architecture, planning, and final judgment; and
- cost-efficient coding models for repository discovery, implementation, debugging, and validation.

The goal is not simply to spend fewer tokens. The goal is to use each model where it creates the most value, preserve context across handoffs, and ship high-quality products without making the implementation model invent decisions it is poorly positioned to make.

## The Problem

Working across several projects with different AI coding tools creates recurring friction.

### Repeated explanation

Every new model or IDE session starts with limited context. The user repeatedly explains:

- what the project does;
- how the repository is organized;
- what has already been built;
- what the current feature should accomplish;
- which architectural choices were made;
- what remains unfinished; and
- what the next agent should do.

This wastes time and tokens while increasing the chance that important details are lost or rewritten differently at every handoff.

### Expensive models doing low-leverage work

A strong model is valuable for understanding ambiguous requirements, comparing approaches, making architecture decisions, identifying tradeoffs, and reviewing whether the correct product was built.

It is often wasteful to use that same model for every repository search, mechanical edit, repetitive test run, and straightforward implementation step.

### Smaller models forced to invent decisions

A cost-efficient coding model can implement complex features well when it receives:

- a clear objective;
- exact boundaries and contracts;
- repository-grounded task packets;
- expected failure behavior;
- test cases and validation commands; and
- explicit stop conditions.

When those are missing, the implementation model must guess. A weak implementation is then often blamed on the model even though the real defect was an incomplete design or plan.

### Context loss between planning and review

The strong model may deeply understand the feature during brainstorming and planning. Implementation then happens in another tool or conversation. When final review begins in a new conversation, the strong model must spend a large part of its context window rediscovering the repository and reconstructing its own earlier reasoning.

That is expensive and leaves less context for the work that matters: inspecting the actual changes and deciding whether they are correct and ready to ship.

### Implementers reviewing their own work

An agent that wrote the code is a poor independent judge of whether the work is complete. It remembers its intent, fills gaps mentally, and can mistake passing focused tests for proof that the complete product works.

### Linear workflows stop too early

A normal checklist often ends at implementation or a green test suite. Shipping requires a loop:

```text
understand -> decide -> plan -> implement -> independently validate -> review -> correct
```

When review finds a problem, the work must return to the earliest stage responsible for that problem instead of applying another patch at the end.

## What This Workflow Solves

The six-stage loop separates responsibilities while keeping one durable chain of evidence.

| Stage | Model and context | Primary responsibility | Main artifact |
|---|---|---|---|
| 0. Discovery | SLM, fresh context | Understand repository reality once | `00-discovery.md` |
| 1. Brainstorming | LLM, persistent main context | Clarify requirements, value, scope, tradeoffs, and architecture | `01-design.md` |
| 2. Planning | Same LLM context | Convert the design into repository-grounded SLM execution packets | `02-plan/` and `02-llm-review-anchor.md` |
| 3. Implementation | SLM, fresh context | Execute the approved tasks with tests, commits, and evidence | `03-implementation-log.md` |
| 4. Validation | Different SLM, fresh context | Independently debug, test, verify, and summarize the final delta | `04-validation-report.md` and `04-return-packet.md` |
| 5. Final review | Return to the Stage 1-2 LLM context | Decide whether the right product was built and is ready to ship | `05-final-review.md` |

Stages 1, 2, and 5 use one continuing LLM conversation. It may be compacted, but it should not be replaced unless unavoidable. Stages 3 and 4 use fresh SLM contexts so implementation and verification remain focused and independent.

## Context Topology

```text
Fresh SLM context A                Persistent LLM context P
Stage 0 discovery ---------------> Stage 1 brainstorm -> Stage 2 plan
                                                        |
                                                        | pause conversation
                                                        v
Fresh SLM context B                Fresh SLM context C
Stage 3 implementation ----------> Stage 4 validation + return packet
                                                        |
                                                        v
                                   Persistent LLM context P
                                   Stage 5 final review
```

The LLM conversation is intentionally preserved because it already contains the reasoning behind the design and plan. Stage 4 returns only the changed state and evidence required for review.

## Why These Stages Exist

### Stage 0: Pay the discovery cost once

Stage 0 maps repository reality before expensive reasoning begins. It finds project instructions, architecture, relevant modules, public contracts, tests, commands, configuration, current progress, documentation drift, and known risks.

Its purpose is not to inventory every file. It creates a high-signal navigation map so the LLM can reason from evidence without spending its context window exploring the entire repository.

### Stage 1: Decide what should be built

Stage 1 turns a rough request into a precise product and architecture design. It challenges the idea, defines the user outcome, compares approaches, resolves tradeoffs, identifies failure behavior, and creates measurable acceptance criteria.

This is where the strongest model creates the most leverage. Better understanding here produces a better plan and therefore a better SLM implementation.

### Stage 2: Remove implementation guesswork

Stage 2 translates the approved design into execution packets. Each task specifies relevant files and symbols, contracts, invariants, implementation guidance, test cases, validation commands, allowed adaptations, and stop conditions.

The planner is accountable for plan quality. If the SLM cannot execute safely without inventing architecture or product behavior, the plan is incomplete.

Before pausing, the LLM writes `02-llm-review-anchor.md`. This preserves the objective, key decisions, rejected alternatives, expected change surface, acceptance criteria, and review hypotheses in case the conversation is compacted.

### Stage 3: Execute efficiently

Stage 3 gives a cost-efficient coding model a bounded task rather than the entire project history. It reads only the plan, relevant design sections, and required source/tests.

The implementer follows a gated cycle:

```text
inspect -> reproduce or write failing test -> implement -> focused verification ->
regression checks -> runtime validation -> diff review -> document -> commit
```

It may make small repository-grounded adaptations, but must return plan or design defects instead of silently redesigning the feature.

### Stage 4: Use a fresh checker

Stage 4 runs in a separate SLM context. It does not trust the implementation report. It compares the plan with the actual diff, reproduces failures, finds root causes, validates negative and runtime paths, and fixes clear implementation defects.

It then writes `04-return-packet.md`, a reviewer-oriented delta dossier containing:

- final commit range and worktree state;
- commit-by-commit intent;
- file and symbol changes;
- contract and architecture deltas;
- acceptance-criterion coverage;
- commands and runtime evidence;
- plan deviations and fixes;
- newly discovered repository facts;
- residual risks and unverified claims; and
- the recommended Stage 5 reading order.

This prevents the final LLM from repeating Stage 0 discovery.

### Stage 5: Review with the original reasoning context

Stage 5 returns to the LLM conversation that created the design and plan. The model already understands why choices were made. It reloads only the review anchor, Stage 4 return packet, actual diff, and high-risk changed files.

This preserves context for product and engineering judgment rather than spending it on repository rediscovery.

## Cost Efficiency Without Quality Loss

The workflow saves cost through specialization, not by reducing rigor.

### Strong-model tokens are spent on high-leverage decisions

The LLM handles:

- ambiguous requirements;
- product scope and user value;
- architecture and system boundaries;
- security, reliability, migration, and compatibility tradeoffs;
- execution-plan quality; and
- final production-readiness judgment.

### SLM tokens are spent on evidence-heavy execution

SLMs handle:

- repository mapping;
- bounded implementation tasks;
- test execution;
- debugging and reproduction;
- diff inspection; and
- evidence collection.

### The workflow avoids repeated full-context loading

Each model loads only the context required for its stage. Durable artifacts carry decisions and deltas across sessions. Large raw outputs remain on disk and are loaded only when needed.

Cost is reduced while preserving independent verification, negative testing, runtime evidence, and final senior review.

## Context Management Model

Context is divided into three layers.

### Hot context

Information needed for the current action:

- the assigned task packet;
- relevant source and tests;
- immediate command output; and
- current errors or runtime behavior.

Keep hot context inside the active model session.

### Warm context

Compact artifacts required to resume or hand off work:

- `STATE.md`;
- discovery and design summaries;
- plan index;
- `02-llm-review-anchor.md`; and
- `04-return-packet.md`.

Warm context is the minimum rehydration set after compaction or context switching.

### Cold evidence

Large or infrequently needed material:

- complete logs;
- screenshots and recordings;
- generated artifacts;
- full test output;
- raw traces; and
- broad unchanged repository files.

Cold evidence is referenced by path and loaded only when a claim needs verification.

The artifacts should not copy the repository. They should summarize decisions and changes while pointing to exact files, symbols, commits, commands, and evidence.

## Quality Safeguards

### Clear decision ownership

The LLM owns consequential product and architecture choices. The SLM owns bounded implementation decisions within those constraints.

### Maker/checker separation

Stage 3 implements. Stage 4 independently validates. The implementer cannot be the only evidence that its work is correct.

### Evidence before completion

Completion claims require fresh tests, commands, runtime checks, generated artifacts, or review evidence. Plans and implementation reports are not proof.

### Happy and negative paths

The workflow requires relevant invalid, empty, permission, dependency-failure, retry, concurrency, compatibility, rollback, and recovery scenarios rather than validating only the happy path.

### Stop conditions instead of guessing

SLM prompts define when minor adaptation is allowed and when work must return to planning or design. This limits silent architecture drift.

### Defect attribution

Every important failure is classified as:

- discovery defect;
- design defect;
- plan defect;
- implementation defect;
- environment defect; or
- verification defect.

This matters because quality problems must improve the stage that caused them. A missing contract in the plan should not be disguised as an implementation mistake.

### Earliest-responsible-stage routing

When review finds a problem, the loop returns to the earliest stage able to correct it:

- misunderstood repository -> Stage 0;
- wrong requirement, tradeoff, or architecture -> Stage 1;
- ambiguous or incomplete execution packet -> Stage 2;
- incomplete or incorrect coding -> Stage 3;
- unexplained failure or insufficient runtime proof -> Stage 4.

This avoids accumulating patches on top of a faulty premise.

## Other Advantages

### Faster feature delivery

The user no longer rewrites a custom handoff prompt for every tool. Each stage has a reusable prompt, known inputs, required output, and exact next action.

### Better instruction following

Every model receives a narrower role with explicit ownership, forbidden decisions, completion gates, and output schemas. Smaller models perform better when their decision surface is constrained.

### Easier interruption and resumption

`STATE.md` records the current stage, commit, artifacts, evidence, blockers, context owner, and next action. A session can stop without losing the workflow.

### Better reviewability

Commits, tasks, acceptance criteria, changed symbols, and validation evidence are linked. Review begins from a structured change manifest rather than a vague summary.

### Honest progress reporting

The workflow distinguishes implemented, validated, release-unverified, blocked, and accepted states. A green unit test is not automatically treated as production readiness.

### Reusable across projects and tools

The prompts do not hardcode a language, framework, product, repository, IDE, or model vendor. They discover and follow each repository's existing conventions.

### Better organizational memory

Design decisions, rejected alternatives, plan assumptions, implementation deviations, and final review findings become durable project artifacts instead of disappearing inside chat histories.

## Canonical Artifact Layout

Use the repository's established documentation convention when one exists. Otherwise use:

```text
docs/ai-workflow/<feature-slug>/
  STATE.md
  00-discovery.md
  01-design.md
  02-plan/
    00-plan-index.md
    01-<slice>.md
    02-<slice>.md
  02-llm-review-anchor.md
  03-implementation-log.md
  04-validation-report.md
  04-return-packet.md
  05-final-review.md
```

The stage number identifies the workflow stage, not the model. A plan may contain one or many implementation slices depending on complexity.

## How To Run The Loop

### 1. Run Stage 0 in an SLM context

Paste [00-discovery.prompt.md](00-discovery.prompt.md). The result should be a repository-grounded discovery artifact and initialized `STATE.md`.

### 2. Start the persistent LLM conversation

Paste [01-brainstorming.prompt.md](01-brainstorming.prompt.md) into the strong reasoning model. Provide the user request and Stage 0 artifact paths.

Continue directly to [02-planning.prompt.md](02-planning.prompt.md) in the same conversation.

### 3. Pause, but preserve, the LLM conversation

Stage 2 writes the plan and `02-llm-review-anchor.md`. Do not close or replace this conversation. Compaction is acceptable because the anchor can restore its critical reasoning.

### 4. Run Stage 3 in a fresh SLM context

Paste [03-implementation.prompt.md](03-implementation.prompt.md) with `STATE.md`, the plan index, and assigned execution packet.

Use another fresh context for a large independent slice when the plan recommends it.

### 5. Run Stage 4 in a different fresh SLM context

Paste [04-validation.prompt.md](04-validation.prompt.md). It should independently inspect the diff, validate behavior, fix localized implementation defects, and create the return packet.

### 6. Return to the original LLM conversation

Paste [05-review.prompt.md](05-review.prompt.md) into the same conversation used for Stages 1 and 2.

Stage 5 should begin with:

1. `STATE.md`;
2. `02-llm-review-anchor.md`;
3. `04-return-packet.md`;
4. the actual commit range and diff; and
5. targeted high-risk files and evidence.

It should not begin by rereading the entire repository.

## Starting From A Later Stage

- New or poorly understood project: start at Stage 0.
- Repository already understood but feature is unclear: start at Stage 1.
- Approved design already exists: start at Stage 2.
- Execution-ready plan already exists: start at Stage 3.
- Code already changed or a bug exists: start at Stage 4.
- Work claims to be complete: start at Stage 5.

Starting late is allowed. Pretending skipped artifacts or evidence exist is not.

If Stage 5 cannot return to the original LLM conversation, start a new strong-model context with this rehydration order:

1. `STATE.md`
2. `02-llm-review-anchor.md`
3. `04-return-packet.md`
4. referenced sections of `01-design.md`
5. `02-plan/00-plan-index.md`
6. actual commit range, diff, and targeted changed files

Use broad repository discovery only when these artifacts are missing, contradictory, or stale.

## Handoff Contract

Every stage updates `STATE.md` and ends with the same minimum envelope:

```text
workflow/stage/status -> context owner -> baseline/current HEAD -> artifacts ->
evidence -> decisions/deviations -> unresolved risks -> defect source ->
next stage -> exact next action
```

If repository policy prevents artifact writes, the agent must emit the complete artifact in its response and mark durable handoff as unresolved. Chat-only handoff is a fallback, not the preferred operating mode.

## Definition Of Success

This workflow succeeds when:

- the user spends less time rewriting prompts and project context;
- the strong model spends most of its context on reasoning and review;
- implementation models receive enough detail to execute without architectural guesswork;
- context switching does not erase decisions or progress;
- every completion claim is supported by evidence;
- quality failures are traced to the stage that caused them;
- the final result solves the intended user problem; and
- the product can be shipped with explicit knowledge of remaining risk.

Cost efficiency is useful only when quality remains intact. If a smaller model produces poor work because the design or plan was incomplete, the workflow must improve the design or plan rather than accepting lower quality as the price of using a cheaper model.
