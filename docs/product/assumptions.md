# Product Assumptions and Decisions

## Confirmed

- ThreadScope's initial release is personal-use only.
- ThreadScope is delivered as a micro-SaaS web application.
- One Owner connects and analyzes their own Threads Account.
- The real acceptance account is the Owner-provided Threads profile `@brnryanino` (`https://www.threads.com/@brnryanino`).
- The first delivery target is a locally runnable, end-to-end website that the Owner can test before any deployment.
- The local acceptance path must connect to the Owner's real Threads Account through the official Meta Threads API; mock data is not an acceptable substitute for acceptance testing.
- Mocked Threads API responses remain required for automated tests and CI and must never be mixed with real account data.
- Remote deployment is out of scope until the local acceptance build has been exercised and accepted.
- GitHub Issues is the authoritative work tracker.
- Application feature implementation requires an approved product and technical specification.

## Hypotheses requiring validation

- The core value is identifying content patterns associated with Threads Account Growth.
- Hook, topic, and conversation classifications will improve the usefulness of deterministic analytics.
- Weekly insights are useful in the personal-use release.
- The official Threads API exposes enough account, post, and reply data for the proposed analysis.
- A modular monolith using the required core stack is sufficient for the personal-use release.

## Unresolved

The remaining decisions are tracked in [GitHub issue #1](https://github.com/brinaryanino/Threadscope/issues/1). API capability hypotheses are tracked in [issue #2](https://github.com/brinaryanino/Threadscope/issues/2).
