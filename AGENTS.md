# ThreadScope Agent Guide

## Start here

Read these sources before making changes:

1. `README.md` for project status and orientation.
2. `MASTER_PROMPT.md` for the authoritative workflow, constraints, required deliverables, and approval gates.
3. `docs/CONTEXT.md` and `docs/GLOSSARY.md` once present for accepted domain language.
4. Relevant accepted files in `docs/adr/` and `docs/specifications/` once present.
5. `docs/delivery/backlog.md` once present; complete one backlog item at a time.

GitHub Issues at `https://github.com/brinaryanino/Threadscope/issues` is the authoritative work tracker. An issue must be decision-complete and labeled `ready-for-agent` before implementation begins.

Accepted ADRs and specifications override hypotheses in `MASTER_PROMPT.md` for the decisions and behavior they govern. The master prompt remains authoritative for workflow, safety constraints, deliverables, and approval gates unless explicitly amended.

## Current phase

ThreadScope is in planning and specification. Do not implement application features until the user explicitly approves the completed product and technical specification.

Use the installed planning skills in this order:

1. `grill-with-docs`, with `grilling` and `domain-modeling`, to resolve requirements and record domain decisions.
2. `to-spec` only after the planning interview reaches shared understanding.
3. Stop after publishing the specification and implementation plan; request explicit approval before feature work.

## Non-negotiable rules

- Use the official Meta Threads API; never invent or present unsupported API capabilities as facts.
- Verify candidate metrics and permissions against official documentation and label them verified, assumed, unavailable, or proof-of-concept pending.
- Never expose OAuth tokens to client-side JavaScript, logs, or unencrypted storage.
- Calculate authoritative metrics deterministically in TypeScript. LLMs may classify or interpret data but must not replace metric calculations.
- Read relevant ADRs before architectural changes and update documentation when a decision changes.
- Preserve explicit missing-data, partial-data, unsupported-metric, and expired-token states.
- Use mocked Threads API responses in standard CI; never require live Meta credentials.
- Keep each change scoped to one GitHub issue and link relevant ADRs, specifications, and pull requests.

## Validation

Once an application scaffold exists, run all documented project checks before marking work complete. The planned command contract is:

```bash
npm run lint
npm run typecheck
npm run test
npm run test:e2e
npm run build
```

If the scaffold adopts different commands, update this file and `docs/delivery/test-strategy.md` with the accepted equivalents. During the documentation-only phase, verify Markdown structure, internal links, source authority, and consistency instead.
