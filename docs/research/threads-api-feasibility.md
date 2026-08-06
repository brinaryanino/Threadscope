# Threads API Feasibility

Status: **In progress**  
Last verified: **6 August 2026**

This document records capabilities verified against current official Meta documentation. It deliberately distinguishes documented behavior from behavior that must be proven with the Owner's Meta app and Threads Account.

## Feasibility summary

The proposed personal-use, read-only analytics path appears feasible for a Threads Tester using the official API. The minimum expected permissions are `threads_basic`, `threads_manage_insights`, and `threads_read_replies`. App Review is not required for a Threads Tester, but would become a release dependency before users without an app role could authorize the app.

Real-account acceptance still requires a proof of concept for the OAuth callback, actual historical pagination, account-specific metric availability, rate limits, token refresh, and representative reply retrieval.

## Verified capabilities

| Area | Verified behavior | Product consequence |
| --- | --- | --- |
| App setup | A Meta app must use the Threads use case. The Threads app ID and matching app secret are used. | The Owner must create/configure a Meta app before real-account acceptance. |
| Personal testing | A Threads Tester may grant permissions without App Review after accepting the tester invitation. | Personal-use local testing can precede App Review. |
| OAuth | Authorization uses `https://threads.net/oauth/authorize`; the returned one-use code is exchanged server-side at `https://graph.threads.net/oauth/access_token`. Codes expire after one hour. | OAuth state validation, exact redirect matching, server-side code exchange, and graceful denial handling are required. |
| Permissions | `threads_basic` is required generally; `threads_manage_insights` reads insights; `threads_read_replies` reads replies. | Do not request publishing or reply-management permissions for the analytics-only release. |
| Tokens | Short-lived tokens last one hour. Unexpired short-lived tokens can be exchanged server-side for 60-day tokens. Long-lived tokens at least 24 hours old can be refreshed before expiry for another 60 days. | Store tokens encrypted server-side, track expiry, schedule refresh, and require reauthorization after expiry. |
| Own profile | The app-scoped profile exposes ID, username, display name, profile picture, biography, and verification state. | Initial sync can store the Owner's supported profile fields. |
| Own posts | `GET /{threads-user-id}/threads` returns a paginated list of posts created by the app-scoped user and accepts `since`, `until`, and `limit`. | Initial sync can page through owned posts and deduplicate by media ID. |
| Post fields | Available fields include ID, media type, text, timestamp, permalink, topic tag, quote/repost relationships, attachments, and carousel children. | The proposed post table and content classification inputs are broadly supported. |
| Post insights | `GET /{threads-media-id}/insights` documents `views`, `likes`, `replies`, `reposts`, `quotes`, and `shares`; metrics are lifetime and nested replies are not included. `views` and `shares` are labeled in development. | Snapshot lifetime values over time; surface development/missing states and never infer nested reply engagement from post totals. |
| User insights | `GET /{threads-user-id}/threads_insights` documents profile views, likes, replies, reposts, quotes, clicks, follower count, and follower demographics. | Dashboard totals and follower snapshots are feasible, subject to account testing. |
| User insight history | `since`/`until` cannot precede 13 April 2024; user insights are not guaranteed before 1 June 2024. `followers_count` does not support `since`/`until`. | Historical follower growth must be generated from future snapshots; do not claim imported follower history. |
| Replies | `GET /{media-id}/replies` returns top-level replies. `GET /{media-id}/conversation` returns a flattened paginated list of top-level and nested replies. | Conversation-quality analysis is technically possible for replies visible to the authorized account. |
| Reply fields | Reply text, username where permitted, timestamp, media type, parent/root IDs, reply ownership, visibility status, and reply audience are documented. | Store only fields required for analysis and account for absent text/user fields. |

## Limits and cautions

- Post `views` and `shares` are explicitly labeled as metrics in development; the UI needs unsupported/missing/delayed states.
- Post insight totals do not capture nested replies' metrics.
- User insight date parameters have documented lower bounds, while follower count is only a current total.
- Follower demographics require at least 100 followers and are unnecessary for the personal-use MVP unless later justified.
- Public-profile discovery has separate permissions, access-level requirements, a 100-follower threshold, and a 1,000-request rolling 24-hour limit. It is outside the personal-use analytics path.
- App Review and app publication are required before people without an app role can grant permissions.
- Official docs require exact valid OAuth redirect URIs but do not explicitly confirm localhost behavior in the reviewed pages.

## Proof-of-concept pending

1. Confirm whether the Meta app accepts the intended localhost callback; if not, use an HTTPS development tunnel while the application itself remains local.
2. Complete OAuth with the Owner added and accepted as a Threads Tester.
3. Verify the exact permissions returned for `threads_basic`, `threads_manage_insights`, and `threads_read_replies`.
4. Exchange and refresh a real long-lived token without exposing it to browser code or logs.
5. Measure how far owned-post pagination goes for the Owner's account and document the observed initial history.
6. Record which documented account and post metrics return values, empty arrays, delayed values, or permission errors.
7. Retrieve top-level and nested conversations from representative posts and measure volume.
8. Observe relevant rate-limit headers/errors and establish a conservative sync budget.
9. Test token expiry, revoked authorization, partial pagination failure, and retry behavior.

## Official sources

- [Get Started](https://developers.facebook.com/documentation/threads/get-started)
- [Get Access Tokens](https://developers.facebook.com/documentation/threads/get-started/get-access-tokens-and-permissions)
- [Long-Lived Access Tokens](https://developers.facebook.com/documentation/threads/get-started/long-lived-tokens)
- [Threads Insights API](https://developers.facebook.com/documentation/threads/insights)
- [Retrieve User Posts](https://developers.facebook.com/documentation/threads/retrieve-and-discover-posts/retrieve-posts)
- [Retrieve User Replies](https://developers.facebook.com/documentation/threads/retrieve-and-manage-replies/retrieve-replies)
- [Retrieve Media Replies and Conversations](https://developers.facebook.com/documentation/threads/retrieve-and-manage-replies/replies-and-conversations)
- [Threads Profiles](https://developers.facebook.com/documentation/threads/threads-profiles)
- [Configure the Threads Use Case](https://developers.facebook.com/documentation/development/create-an-app/threads-use-case)
