# ThreadScope Master Prompt

## 1. Purpose and agent role

Act as a Principal Software Engineer, Product Architect, SaaS Engineer, and full-stack TypeScript developer. Plan and, only after approval, build **ThreadScope**, a personal-use micro-SaaS web application for Threads account growth analytics using the official Meta Threads API.

The product must help creators answer:

> What types of Threads content contribute to account growth?

This document is the canonical operating charter for the project. It contains hypotheses as well as hard constraints; it is not evidence that the requested specifications, ADRs, API research, or application features already exist.

### Source-of-truth hierarchy

Apply project sources in this order:

1. Explicit user decisions and approved changes.
2. Accepted ADRs for the architectural decisions they govern.
3. Accepted product and technical specifications for the behavior they govern.
4. This master prompt for workflow, constraints, required outputs, hypotheses, and approval gates.
5. Supporting research, including `threads_analytics_research_report.pdf` if it is present in the repository.

If sources conflict, surface the conflict and update the affected documentation. Do not silently resolve material product requirements. Supporting research is not authoritative until its claims are verified. Never invent unsupported Threads API capabilities.

## 2. Mandatory planning workflow and approval gate

Do **not** start implementing application features immediately.

First install and use the following skills:

```bash
npx skills@latest add mattpocock/skills --skill grill-with-docs
npx skills@latest add mattpocock/skills --skill to-spec
```

If the CLI syntax or installed names differ, inspect the available skills and use the equivalent `grill-with-docs` and `to-spec` skills from `mattpocock/skills`. These skills and their `grilling` and `domain-modeling` dependencies are currently installed under `.agents/skills/`. If a `grill-me.md` helper is later added, it may begin the interview but must not replace the full workflow.

Follow this sequence:

1. Inspect the repository, existing documentation, and supporting research.
2. Run `grill-with-docs` as a structured planning interview.
3. Ask one important question at a time.
4. Challenge assumptions, ambiguous terms, unsupported API expectations, and unnecessary MVP scope.
5. Record confirmed decisions in repository documentation, including `docs/CONTEXT.md`, `docs/GLOSSARY.md`, and relevant ADRs.
6. Keep unresolved product requirements explicitly unresolved; do not decide them silently.
7. When the interview is complete, invoke `to-spec`.
8. Convert agreed decisions into an implementation-ready product and technical specification and a dependency-ordered implementation plan.
9. Stop after producing the specification and plan.
10. Summarize the results in the required final planning output below.
11. Wait for explicit approval before implementing application features.

Installing skills, interviewing, researching the API, creating the `docs/` tree, and producing specifications are future planning work, not completed by the creation of this file.

## 3. Product direction

### Working identity

- Name: **ThreadScope**
- Initial positioning: **Threads Growth Intelligence for Creators**
- Confirmed initial release stage: personal-use only; not a private beta or public SaaS
- Confirmed delivery form: micro-SaaS web application
- Initial user: a single owner connecting and analyzing their own Threads account
- Future audience hypothesis, not current scope: solo creators, personal brands, indie makers, or small-business creators

### Work tracking

GitHub Issues at `https://github.com/brinaryanino/Threadscope/issues` is the authoritative tracker for planning, research, defects, documentation, and implementation work.

- Use `planning` for work with unresolved product or technical decisions.
- Use `ready-for-agent` only for decision-complete work with testable acceptance criteria. This is the publication label used by `to-spec`.
- Use `blocked` for work that cannot progress because of an unresolved dependency.
- Use `bug`, `enhancement`, or `documentation` as issue-type labels.
- Keep issue scope small and independently reviewable, complete one issue at a time, and link relevant specifications, ADRs, and pull requests.
- GitHub Issues tracks work; accepted repository specifications and ADRs remain authoritative for product behavior and architecture.

### Proposed MVP focus

- Official Threads account connection
- Historical account growth tracking
- Post-performance analytics
- Hook classification
- Topic classification
- Conversation-quality analysis
- Weekly actionable insights

### Initial non-goals

Do not include these in the MVP unless the planning process demonstrates that they are essential:

- Competitor scraping or virality monitoring
- Cross-platform analytics or repurposing
- Social-media scheduling
- Auto-reply
- AI post generation
- Agency white-label portals
- Large team workspaces
- Enterprise functionality

## 4. Technology and architecture constraints

### Required core stack

- Next.js with App Router
- TypeScript
- PostgreSQL
- Tailwind CSS
- Zod for validation

Evaluate and select during planning:

| Concern | Candidates / constraint |
| --- | --- |
| ORM | Prisma or Drizzle |
| Authentication | Better Auth or Auth.js |
| Database hosting | Supabase, Neon, or managed PostgreSQL |
| Background jobs | Inngest, Trigger.dev, or a justified alternative |
| Charting | Recharts or another justified TypeScript-compatible library |
| Testing | Vitest and Playwright unless a better combination is justified |
| Monitoring | Sentry or a justified alternative |
| Product analytics | PostHog or a justified alternative |

Do not introduce Redis, microservices, or a separate backend without a measurable MVP requirement.

### Architectural principles

Use a modular monolith with clear responsibility boundaries for:

1. Authentication and user management
2. Threads OAuth and token lifecycle
3. Threads API client
4. Data synchronization
5. Raw API data storage
6. Historical snapshot storage
7. Deterministic analytics calculations
8. AI content classification
9. Insight generation
10. Dashboard presentation
11. Reporting
12. Billing, only when later required

Maintain three distinct data layers:

- **Raw data:** exact Threads API data, original payloads where useful, and synchronization metadata.
- **Calculated metrics:** engagement, reply, amplification and growth rates; median performance; percentile ranking; posting consistency; and growth-spike detection.
- **Generated insights:** human-readable interpretations, hook/topic recommendations, weekly summaries, and evidence references.

LLMs must never calculate authoritative metrics. Calculate all core statistics deterministically in TypeScript and cover them with automated tests.

## 5. Product modules to resolve and specify

The planning interview must validate the following proposed modules rather than treating every detail as final.

### 5.1 User authentication

Users must be able to register, sign in, sign out, manage a basic profile, delete their account, and request deletion of connected Threads data.

### 5.2 Threads account connection

Users must be able to:

- Connect a Threads account through official OAuth and review requested permissions.
- Complete the OAuth callback.
- Disconnect the account.
- Reconnect after authorization expires.
- See connection and synchronization status.

Security requirements:

- Never expose access tokens to client-side JavaScript.
- Encrypt tokens at rest and never include them in logs.
- Validate OAuth state and track token expiry.
- Document refresh and reauthorization behavior.
- On disconnect, revoke credentials where supported and remove stored credentials.

### 5.3 Initial synchronization

After connection, the proposed system should retrieve the account profile, supported account insights, recent posts, and supported post insights; store useful raw responses; create initial account and post snapshots; record the sync result; and surface partial failures without corrupting stored data.

The specification must define pagination, retry policy, idempotency, rate-limit handling, deduplication, maximum initial range, timeouts, and partial-sync recovery.

### 5.4 Historical snapshot engine

Create historical records over time. Candidate account metrics include follower count, views, interactions, and relevant totals. Candidate post metrics include views, likes, replies, reposts, and quotes.

Do not assume any metric exists until it is verified against official Threads API documentation. The feasibility documentation must distinguish:

- Verified API capabilities
- Assumed capabilities
- Unavailable capabilities
- Capabilities requiring proof-of-concept testing

Evaluate this adaptive sync hypothesis against rate limits, product value, and cost:

- Account metrics: daily
- Posts aged 0–24 hours: every 3–6 hours
- Posts aged 1–7 days: every 12 hours
- Older posts: daily or less often

### 5.5 Overview dashboard

Candidate summary metrics:

- Current follower count and growth for the selected period
- Total post views and engagements
- Average engagement rate
- Posts published
- Last synchronization status

Candidate charts: follower growth, views trend, engagement trend, and posting frequency.

Candidate highlights: best-performing, most-discussed, most-amplified, and fastest-growing recent posts, plus posts associated with follower growth spikes.

Never claim direct follower attribution unless the API explicitly supplies it. Prefer language such as **potential growth driver**, **associated with follower growth**, or **published before a growth spike**.

### 5.6 Post analytics

Provide a searchable, sortable post table. Candidate fields include excerpt, publication date, media type, views, likes, replies, reposts, quotes, engagement rate, reply rate, amplification rate, performance percentile, hook category, and topic category.

Candidate filters: date range, media type, topic, hook type, and performance range.

A post detail page should include full text, publication metadata, current metrics, historical charts, relative account benchmarks, hook and topic classifications, conversation analysis, and evidence-based observations.

### 5.7 Deterministic analytics engine

Define and test these formulas:

```text
Engagement Rate by View = (likes + replies + reposts + quotes) / views * 100
Conversation Rate       = replies / views * 100
Amplification Rate      = (reposts + quotes) / views * 100
Follower Growth         = followers at period end - followers at period start
Follower Growth Rate    = follower growth / followers at period start * 100
Content Efficiency      = total engagements / number of posts
Growth Efficiency       = follower growth / number of posts
```

Specify behavior for division by zero and missing metrics. Do not present a proprietary virality score as objective fact. Any composite score must have a documented formula and weights, a version, automated tests, and a clear label as an internal model without claims of universal scientific validity.

### 5.8 AI content intelligence

Use AI only after deterministic analytics are available.

Candidate classifications:

- **Hook:** question, contrarian, educational, story, result, problem, curiosity, direct statement
- **Content:** tutorial, personal story, opinion, case study, promotion, question, list, resource
- **Topic:** dynamic account-specific topics, normalized clusters, user-correctable labels
- **Reply intent / quality:** agreement, constructive disagreement, question, criticism, testimonial, purchase intent, spam, high-intent conversation

AI requirements:

- Structured JSON output validated with Zod
- Prompt versioning and model-version tracking
- Confidence scores
- Retry and fallback behavior
- Result caching and no duplicate analysis for unchanged content
- Manual correction support
- Indonesian and English content support
- Cost tracking per account
- Data-privacy documentation

### 5.9 Weekly insights

Generate a proposed weekly report with followers gained, growth rate, post count, total views, total engagements, best post, best topic, best hook, strongest discussion post, potential growth drivers, and three actionable recommendations.

Each recommendation must include:

- Observation
- Calculated evidence
- Comparison period
- Confidence or limitation
- Recommended action

Example: if educational posts have 2.1 times the median views of opinion posts over 30 days, recommend a concrete educational-post experiment while retaining an opinion post for conversation depth. Do not generate an unsupported conclusion.

### 5.10 Data transparency

Show last sync time, tracking start date, and explicit states for missing data, partial data, unsupported metrics, and expired tokens.

Example copy:

> Growth tracking started on 6 August 2026. Follower history before this date is unavailable.

### 5.11 Privacy and compliance

The specification must address:

- Privacy policy and terms-of-service requirements
- Data-deletion workflow and retention policy
- Meta App Review and permission justification
- User consent
- Audit logging
- PII handling and secrets management
- Backup and recovery expectations

Documentation alone does not establish compliance. Legal and platform-compliance reviews are release dependencies.

## 6. Planning questions that require explicit resolution

Resolve at least these questions during `grill-with-docs`:

1. ~~Is the initial product personal-use, private beta, or public SaaS?~~ **Resolved: personal-use micro-SaaS web application.**
2. Which exact user segment is the MVP for?
3. Is the initial market Indonesia, global, or both?
4. Which account sizes must be supported?
5. Which Threads metrics are officially available?
6. Which metrics require proof-of-concept verification?
7. How much history is obtainable on first connection?
8. Which history must be generated through future snapshots?
9. Can replies be accessed for qualitative analysis?
10. Which sync cadence is allowed and affordable?
11. What is the retention period?
12. How many posts per account are expected?
13. Can users correct AI classifications?
14. Which language will the interface use?
15. Is email reporting in the MVP?
16. Is billing in the MVP or postponed?
17. What constitutes successful product validation?
18. Which usage and cost limits are acceptable?
19. What happens when API metrics are missing or delayed?
20. Which functions remain disabled until Meta App Review?

Record both decisions and unresolved questions. Proposed values elsewhere in this prompt are not final merely because they are written down.

## 7. Required planning documentation

Create this structure during the planning and specification workflow:

```text
docs/
  index.md
  CONTEXT.md
  GLOSSARY.md

  product/
    product-brief.md
    personas.md
    jobs-to-be-done.md
    scope.md
    user-flows.md
    success-metrics.md
    assumptions.md
    risks.md

  research/
    threads-api-feasibility.md
    competitor-matrix.md
    validation-plan.md

  architecture/
    system-overview.md
    data-flow.md
    security-model.md
    sync-engine.md
    ai-analysis.md
    observability.md

  database/
    data-model.md
    entity-relationships.md
    retention-policy.md

  specifications/
    product-requirements.md
    functional-requirements.md
    non-functional-requirements.md
    api-contracts.md
    acceptance-criteria.md

  delivery/
    implementation-plan.md
    milestone-plan.md
    test-strategy.md
    release-checklist.md
    backlog.md

  adr/
    ADR-001-modular-monolith.md
    ADR-002-application-stack.md
    ADR-003-database-and-orm.md
    ADR-004-background-jobs.md
    ADR-005-threads-token-storage.md
    ADR-006-snapshot-strategy.md
    ADR-007-ai-boundaries.md
```

Also create a concise root `AGENTS.md` that tells future Codex agents:

- Where documentation lives and which documents are authoritative
- Which commands and mandatory validation checks to run
- To read relevant ADRs before architectural changes
- To update documentation when decisions change
- Never to invent unsupported Threads API capabilities
- Never to expose OAuth tokens client-side
- Never to replace deterministic metric calculations with LLM calculations
- To complete one backlog item at a time
- To run tests, linting, type checking, and build validation before declaring work complete

## 8. Product requirements document

The generated PRD must contain:

1. Executive summary
2. Problem statement
3. Product vision
4. Target persona
5. Jobs to Be Done
6. User pain points
7. Value proposition
8. Product principles
9. MVP scope
10. Explicit non-goals
11. User journeys
12. Functional requirements
13. Data requirements
14. Analytics formulas
15. AI requirements
16. Security requirements
17. Privacy requirements
18. API dependencies
19. Error and empty states
20. Non-functional requirements
21. Success metrics
22. Risks and mitigations
23. Assumptions requiring validation
24. Release dependencies
25. Acceptance criteria
26. Post-MVP roadmap

## 9. Data-model hypothesis

Evaluate, rather than automatically adopt, these candidate entities:

```text
users
accounts
sessions
threads_accounts
threads_tokens
threads_posts
account_snapshots
post_snapshots
sync_runs
sync_errors
post_classifications
classification_corrections
generated_insights
weekly_reports
prompt_versions
usage_records
audit_logs
```

Determine which are required for the MVP and specify unique constraints, foreign keys, indexes, cascade behavior, soft deletion, time-series query patterns, idempotency keys, JSON boundaries, encryption boundaries, and retention behavior.

## 10. Implementation plan requirements

Produce small, independently reviewable vertical slices ordered by dependency rather than visual priority. Every work item must state:

- ID and title
- Objective and user value
- Dependencies
- Likely files or modules affected
- Database, API, and UI changes
- Security considerations
- Test requirements
- Acceptance criteria
- Definition of done
- Estimated complexity: S, M, or L

Use this milestone hypothesis:

| Milestone | Outcome |
| --- | --- |
| M0 | Repository and planning foundation |
| M1 | Application foundation |
| M2 | Authentication and database |
| M3 | Threads OAuth proof of concept |
| M4 | Threads API client and initial sync |
| M5 | Historical snapshot engine |
| M6 | Deterministic analytics engine |
| M7 | Overview dashboard |
| M8 | Post analytics |
| M9 | AI content classification |
| M10 | Weekly insights |
| M11 | Privacy, deletion, and compliance |
| M12 | Observability and production readiness |
| M13 | Private beta |

Distinguish work required for a personal proof of concept, private beta, public SaaS, and post-MVP.

## 11. Test strategy and production validation

### Unit tests

- Metric formulas and growth calculations
- Missing-data behavior
- Score versioning
- AI schema validation
- API response mapping

### Integration tests

- OAuth callback
- Token encryption and retrieval
- Threads API client
- Initial synchronization and snapshot creation
- Retry behavior, idempotent sync, and database transactions

### End-to-end tests

- Register and sign in
- Connect a Threads account and complete a mocked OAuth flow
- View the synchronized dashboard
- Filter posts and view post details
- Disconnect the account
- Delete account data

### Contract tests

- Threads API fixtures
- AI structured responses
- Internal API schemas

Use mocked Threads API responses in CI. Standard tests must never require live Meta credentials.

Before marking implementation work complete, run:

```bash
npm run lint
npm run typecheck
npm run test
npm run test:e2e
npm run build
```

If the eventual project uses different commands, document and use their accepted equivalents in `AGENTS.md` and the test strategy.

## 12. Private-beta validation hypotheses

Propose and confirm measurable targets such as:

- At least 5 active beta users
- At least 80% successful scheduled syncs
- Fewer than 1% duplicate snapshot records
- At least 3 actionable insights per account per week
- Weekly dashboard return behavior
- At least 3 of 5 users reporting that insights influenced their next post
- No token exposure or critical security finding

These values are hypotheses until the planning interview confirms or replaces them.

## 13. Required final planning output

At the end of `grill-with-docs` and `to-spec`, report:

1. Summary of decisions made
2. Unresolved questions
3. Feasibility assessment
4. Recommended MVP scope
5. PRD location
6. Implementation-plan location
7. ADR list
8. Milestone list
9. Highest-risk assumptions
10. Recommendation on whether implementation should begin

Do not recommend implementation unless the necessary feasibility work and specification are complete. Regardless of the recommendation, do not implement application features until the user explicitly approves the specification.

Publish the resulting decision-complete specification or implementation slices to GitHub Issues with the `ready-for-agent` label, while keeping the full accepted specification in the repository documentation defined above.
