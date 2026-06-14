# Khata Baseline Workflow Audit

Audit date: 2026-06-14

Audited cycle: `/Users/abhishek/python_venv/khata_app/docs/ai-workflow/khata-app-baseline`

Repository HEAD during audit: `837ccbc0cfdb09a25b6aad02e4b0c357abafa8a6`

## Verdict

The six-stage chain worked and produced useful product-quality improvements, but the cycle lifecycle was not robust for reuse.

- Stages 1-3 created a strong design, detailed plan, and traceable implementation commits.
- Stage 4 independently found test/fixture defects and produced a useful return packet.
- Stage 5 did not rubber-stamp Stage 4; it found additional product and PDF defects, fixed them, and correctly withheld production-release approval.
- The cycle ended `code-ready-for-phone-testing; production-release-blocked`, not fully production ready.
- The later Stage 0 refresh reused and rewrote the closed `khata-app-baseline` folder, resetting its active state. This is the main multi-cycle workflow defect.

## Stage Audit

| Stage | Result | Audit finding |
|---|---|---|
| 0 initial discovery | Partial but useful | Established baseline and repository map. |
| 1 design | Worked well | Defined GST/non-GST behavior, immutable snapshots, date-only flow, PDF/share behavior, and acceptance criteria. |
| 2 planning | Worked well | Six executable task packets, review anchor, risk gates, and validation commands. |
| 3 implementation | Worked with honest gaps | Tasks 01-06 committed separately; mobile tests/build passed; PostgreSQL and Android runtime evidence remained blocked. |
| 4 validation | Useful but too optimistic | Found stale hash test and duplicate fixture key. It correctly recorded environment gaps, but missed archived-customer filtering and several PDF product/layout problems. |
| 5 final review | Worked as intended | Used stronger reasoning to find Stage 4 misses, improve PDFs and balance filtering, run emulator evidence, and give a non-production verdict. |
| Post-review audit | Valuable but outside the core numbering | Added backup/catalog/analytics/runtime evidence. This should update Stage 5 evidence or be an explicit audit appendix, not create ambiguous cycle state. |
| 0 refresh | Workflow defect | With no concrete request, it rewrote the closed cycle's discovery and `STATE.md` as if a new Stage 1 should begin. |

## What Worked

### Strong artifact chain

All expected artifacts exist: discovery, design, plan packets, review anchor, implementation log, validation report, return packet, and final review.

### Traceable implementation

Tasks were implemented through focused commits from `7a961b2` to `3f71e22`. The implementation log records tests, deviations, and blocked evidence.

### Effective maker/checker split

Stage 4 independently reran tests and found defects instead of trusting Stage 3. Stage 5 then challenged Stage 4 and found additional issues, proving the value of returning to the stronger planning context.

### Honest release verdict

The final review did not equate a successful APK build with production readiness. It retained blockers for signing/application identity, physical-device behavior, PostgreSQL integration, and Unicode PDFs.

## Problems Found

### P1: Closed-cycle state was reset

`STATE.md` currently says:

- `Current stage: 0-discovery`
- `Last completed stage: 0-discovery (refresh)`
- `Next required stage: 1-brainstorming`

The same file also retains the completed baseline objective and Stage 1-5 history. This makes the folder simultaneously look closed and newly active.

Required correction: preserve the baseline cycle as closed historical evidence. A new feature must create a new linked cycle with its own objective and `STATE.md`.

### P1: No project-level cycle registry

There is no `docs/ai-workflow/INDEX.md` or `PROJECT_CONTEXT.md`. A future Stage 0 must scan folders or guess which artifact is current.

Required correction: maintain a compact registry and verified rolling project context above individual cycles.

### P1: Empty Stage 0 input inferred a new delivery

The refresh had no feature request, but changed the old cycle to prepare Stage 1 for a future scope decision.

Required correction: use a separate `context-refresh` mode. It may reconcile project memory but cannot open or reset a feature cycle without a concrete objective.

### P2: Final evidence spans multiple states

Stage 4's return packet ends at `de7318a` plus uncommitted fixes. Stage 5 and the production audit describe later changes included at `837ccbc`. Historical reports are valid, but a future cycle needs one project-level record explaining the final accepted code state and remaining release blockers.

Required correction: Stage 5 closes the registry row at the final reviewed SHA and promotes verified facts to `PROJECT_CONTEXT.md`.

### P2: Stage 4 verdict understated product gaps

Stage 4 returned `pass-with-minor-issues`, but Stage 5 later found important archived-customer and PDF layout/content issues.

This does not mean Stage 4 failed completely; it shows its validation packet lacked sufficiently strong product-level visual and domain review. The updated prompts should retain Stage 5 as the stronger product/architecture judge.

### P2: Post-review audit created lifecycle ambiguity

`06-local-production-audit.md` contains meaningful fixes and evidence after the core Stage 5 review.

Required correction: append/update Stage 5 evidence or store a clearly named audit appendix while keeping the cycle sealed at its final reviewed SHA/status. Do not reset the cycle or silently extend core stage numbering.

### P3: Documentation drift remains visible

The refreshed discovery reports stale PDF-threshold and test-count claims in module docs. This is correctly detected, but project-level context should carry the authoritative current rule and keep the drift listed until fixed.

## Recommended Registration Of This Legacy Cycle

Suggested project registry entry:

| Cycle ID | Type | Objective | Status | Baseline SHA | Final SHA | Affected areas | Artifact path |
|---|---|---|---|---|---|---|---|
| `khata-app-baseline` | legacy/feature | GST/non-GST invoices, date-only creation, adaptive PDFs, sharing, balance summaries | code-ready-for-phone-testing; production-release-blocked | `7699ae6` | `837ccbc` | backend invoice/profile/migrations; mobile invoice/PDF/share/balance/backup/catalog | `docs/ai-workflow/khata-app-baseline/` |

The next release-hardening request should create a new follow-up cycle, for example:

```text
docs/ai-workflow/cycles/20260614-android-release-hardening/
```

It should link `khata-app-baseline` as its parent/relevant cycle and inherit only confirmed capabilities and unresolved release blockers.

## Prompt Changes Resulting From This Audit

- Stage 0 now reads project workflow memory before broad repository exploration.
- Added first/new/resume/follow-up/divergent/context-refresh classification.
- Added prior-cycle relevance selection, git freshness checks, and inheritance statuses.
- Added project-level `INDEX.md` and `PROJECT_CONTEXT.md` schemas.
- New feature cycles receive unique directories and lineage links.
- Closed cycles cannot be reset for future work.
- Stage 1 and Stage 2 explicitly consume inherited decisions and protect prior accepted capabilities.
- Stage 4 recommends verified project-context updates.
- Stage 5 seals cycles and promotes only proven current facts into project memory.
