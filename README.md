# Six-Stage LLM-to-SLM Delivery Chain

This system splits expensive reasoning from execution without lowering quality.

| Stage | Recommended model | Owns | Must not own |
|---|---|---|---|
| 1. Discovery | Cost-efficient model with good tools | Repository truth and context map | Product or architecture decisions |
| 2. Brainstorming | Strong reasoning model | Requirements, tradeoffs, design, acceptance criteria | Implementation |
| 3. Planning | Strong reasoning model | Architecture-to-task translation and SLM-ready execution packets | Production code |
| 4. Implementation | Cost-efficient coding model | Faithful task execution and evidence | Consequential redesign |
| 5. Debugging | Fresh cost-efficient coding model | Reproduction, root-cause fixes, plan-compliance validation | Rubber-stamping the implementer |
| 6. Review | Strong reasoning model | Final product/technical judgment and upstream feedback | Accepting claims without evidence |

## Why The Chain Works

Quality is protected by four mechanisms:

1. **Durable context:** every stage reads and updates the same `STATE.md`; chat history is optional.
2. **Decision ownership:** strong models make consequential product and architecture decisions; SLMs execute bounded packets.
3. **Maker/checker split:** implementation and validation use fresh contexts.
4. **Defect attribution:** failures are classified as discovery, design, plan, implementation, environment, or verification defects. A weak plan is sent back to planning instead of being blamed on the implementation model.

## Canonical Artifact Layout

Use the repository's established convention when one exists. Otherwise use:

```text
docs/ai-workflow/<feature-slug>/
  STATE.md
  01-discovery.md
  02-design.md
  03-plan/
    00-plan-index.md
    01-<slice>.md
    02-<slice>.md
  04-implementation-log.md
  05-debug-report.md
  06-final-review.md
```

The stage number identifies the workflow stage, not the model. Plans may contain one or many implementation slices.

## Starting And Skipping Stages

- New or poorly understood project: start at Stage 1.
- Known repository, unclear feature: start at Stage 2.
- Approved design already exists: start at Stage 3.
- Execution-ready plan exists: start at Stage 4.
- Code already changed or a bug exists: start at Stage 5.
- Work claims to be complete: start at Stage 6.

Every prompt includes a recovery protocol. Starting late is allowed; pretending skipped artifacts exist is not.

## Using The Prompts

Paste one stage prompt into the selected model and replace only the input placeholders you know. The agent should discover paths and commands from the repository rather than requiring a long custom preamble.

Do not paste all six prompts into one context. The fresh-context boundaries are part of the quality system.

Each stage leaves the same minimum handoff envelope in `STATE.md` and its final response:

```text
workflow/stage/status -> baseline and current HEAD -> artifacts -> evidence ->
decisions/deviations -> unresolved risks -> defect source -> next stage -> exact next action
```

If repository policy or permissions prevent writing workflow artifacts, the agent must emit the complete artifact in its response and mark durable handoff as unresolved. A chat-only handoff is a fallback, not the preferred path.

When Stage 6 returns work upstream, run the earliest stage capable of correcting the defect:

- misunderstood repository -> Stage 1
- wrong requirement/tradeoff/architecture -> Stage 2
- ambiguous or incomplete execution packet -> Stage 3
- incomplete or incorrect coding -> Stage 4
- unexplained failure or missing runtime proof -> Stage 5
