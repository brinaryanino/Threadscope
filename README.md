# ThreadScope

ThreadScope is a personal-use micro-SaaS web application that helps its owner understand which content patterns contribute to growth on their Threads account. It will use the official Meta Threads API and will not rely on unauthorized scraping.

> **Project status:** planning and specification only. Do not implement application features until the product and technical specification has been explicitly approved.

The confirmed delivery sequence is **local first**: produce a runnable end-to-end acceptance build for the Owner to test, then consider deployment only after the local build is accepted.

## Start here

[`MASTER_PROMPT.md`](./MASTER_PROMPT.md) is the authoritative operating charter for planning and building ThreadScope. It defines the product hypothesis, constraints, required planning interview, documentation outputs, architectural principles, validation requirements, and approval gates.

Current repository inputs:

- [`MASTER_PROMPT.md`](./MASTER_PROMPT.md) — canonical planning and delivery instructions
- [`AGENTS.md`](./AGENTS.md) — concise operating rules for future coding agents
- [`docs/index.md`](./docs/index.md) — planning documentation index and current decision status
- [`.agents/skills/`](./.agents/skills/) — installed planning skills required by the master prompt
- [`skills-lock.json`](./skills-lock.json) — installed-skill lock file

The previously referenced `grill-me.md` helper and `threads_analytics_research_report.pdf` are not currently present. If the research report is restored, treat it as supporting research and validate its claims before considering them product or API facts.

The detailed `docs/` tree, specifications, and ADRs described in the master prompt are required future outputs. Their presence must not be assumed until they have actually been created and reviewed.

## Product direction

The working positioning is **Threads Growth Intelligence for Creators**. The first release is explicitly personal-use only, operated for a single owner rather than offered as a private beta or public SaaS. Solo creators, personal brands, indie makers, and small-business creators remain a possible future audience to validate before expanding distribution.

> What types of Threads content contribute to account growth?

The proposed MVP centers on official account connection, historical snapshots, deterministic post analytics, hook and topic classification, conversation-quality analysis, and weekly actionable insights.

## Work tracking

Use [GitHub Issues](https://github.com/brinaryanino/Threadscope/issues) as the authoritative work tracker. Product questions, research tasks, defects, documentation changes, and implementation slices must be represented by issues before work begins.

Current planning sequence:

1. [Complete the planning interview](https://github.com/brinaryanino/Threadscope/issues/1)
2. [Verify official Threads API feasibility](https://github.com/brinaryanino/Threadscope/issues/2)
3. [Produce the approved product and technical specification](https://github.com/brinaryanino/Threadscope/issues/3)

- Apply `planning` while an issue still requires a product or technical decision.
- Apply `ready-for-agent` only when an issue is decision-complete and has testable acceptance criteria.
- Apply `blocked` when progress depends on an unresolved external or product dependency.
- Add `bug`, `enhancement`, or `documentation` to describe the type of work.
- Complete one issue at a time and link specifications, ADRs, and pull requests from the issue.

## Non-negotiable constraints

- Use only supported capabilities of the official Meta Threads API; never invent metrics or imply unsupported follower attribution.
- Keep OAuth tokens server-side, encrypted at rest, absent from logs, and inaccessible to client-side JavaScript.
- Calculate authoritative statistics deterministically in TypeScript with automated tests; LLMs may interpret metrics but must not calculate the source of truth.
- Start with a modular monolith using Next.js App Router, TypeScript, PostgreSQL, and Tailwind CSS.
- Treat legal compliance and Meta App Review as release dependencies, not as accomplished facts.
- Record unresolved requirements explicitly instead of silently choosing an answer.

## Required workflow

1. Read [`MASTER_PROMPT.md`](./MASTER_PROMPT.md) and inspect the repository and supporting research.
2. Use the installed `grill-with-docs` and `to-spec` skills and their installed dependencies.
3. Run a structured, one-question-at-a-time planning interview and record decisions in project documentation.
4. Verify Threads API capabilities against official documentation and separate verified, assumed, unavailable, and proof-of-concept capabilities.
5. Convert agreed decisions into the required product and technical specification, ADRs, and implementation plan.
6. Publish decision-complete implementation slices to GitHub Issues with the `ready-for-agent` label.
7. Stop and obtain explicit approval before implementing application features.

## Planned core stack

- Next.js with App Router
- TypeScript
- PostgreSQL
- Tailwind CSS
- Zod

ORM, authentication, hosting, background jobs, charting, testing, monitoring, and product analytics choices remain planning decisions. See the master prompt for candidates and selection criteria.

## Documentation authority

Once created and accepted, specific approved specifications and ADRs override earlier hypotheses in `MASTER_PROMPT.md`. The master prompt remains authoritative for workflow, safety constraints, required deliverables, and approval gates unless an explicit project decision amends it.
