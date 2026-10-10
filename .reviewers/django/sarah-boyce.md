# Sarah Boyce: evidence-based review lens

This is an independent synthesis of public review feedback, for preparing and refining your own Django patches. It is not Sarah Boyce speaking, an endorsement, or a prediction of her next review. Use it with the [shared review guidance](README.md), including Django's AI disclosure and human verification requirements. Keep automated reviews local or on your own fork; do not request them on upstream Django pull requests.

Research date: **2026-10-10**. GitHub identity: **[@sarahboyce](https://github.com/sarahboyce)**. Django's [official teams page](https://www.djangoproject.com/foundation/teams/) currently lists her as a Fellow, Merger, Releaser, and member of the triage/review and security teams. These are concrete reasons to include her review perspective; this profile does not rank community respect.

## Sample and limits

The corpus contains **85 Sarah-authored inline comments across 12 distinct PRs**, dated **2024-07-08 through 2026-10-09**. It spans caching, signing/sessions, templates, GIS, mail, exception reporting, file behavior, CSP, admin JavaScript, and management commands. Selection started with recent public comments and searches for PRs reviewed by this account in 2024 and 2025, then deliberately included older and technically varied work. Complete paginated inline-comment lists, PR descriptions, relevant diff hunks, and discussion replies were inspected. PR creation year is not necessarily the review year.

This is a qualitative, purposive sample, not a frequency census. Large mail and template reviews contribute many comments; that volume is not a reliable measure of relative importance. General PR discussion, Trac, and forum history were not systematically sampled. Some PRs remained unmerged when fetched, and inline comments can refer to superseded patch versions. The observations concern review practices, not personality or subject-matter expertise in every subsystem.

## Recurring priorities

### Tests should demonstrate the changed behavior

**High confidence.** The recurring question is whether a test would actually notice the implementation being wrong, rather than whether a test merely exists. In the exception-reporter review, Sarah pointed out a FILES handling change could be reverted without failures, then supplied a missing files case that exposed another failure while accessing POST data. In the modern-mail review, she identified multiple implementation changes that could be reverted or simplified without test failures. In the earlier cache review, she proposed observing whether the view ran again to prove a cache hit, rather than only checking that keys existed. See evidence E1, E3, and E5.

For an agent, this translates to tracing the assertion back to the promised behavior. Prefer a small reproducer that fails on the old implementation and passes on the patch. If an implementation branch has no observable assertion, explain the uncovered behavior. Do not mechanically demand one test per line or presume that every internal simplification needs another test.

### Follow counterpart paths and subclass behavior

**High confidence.** She explicitly requested an async test for signed-cookie sessions, additional async cache methods affected by overrides, and both `startproject` and `startapp` coverage for a template-size override. The cache discussion also distinguishes fixing subclass inheritance from degrading native async implementations when methods are not overridden. See E2, E6, and E12.

The useful lens is to enumerate the interfaces sharing the changed behavior: sync/async, GET/POST/FILES, base/subclass, and command variants. Check which really share the mechanism. Missing counterpart coverage is actionable when the same defect can occur there; a broad speculative testing matrix is not.

### Make compatibility and deprecation plans executable

**High confidence.** In multiline-template work she asked for a warning test, deprecation documentation, version annotations, and comments explaining the exact post-deprecation updates. She questioned a warning that could only be silenced by selecting the new behavior and adding an already-deprecated option to newly generated projects. In mail work she actually followed removal instructions and reported the tests that failed, questioned a documented return-type change, and checked preserved import paths. The GDAL discussion considers precedent and observed usage before deciding how to communicate renamed internals. See E3, E4, and E7.

An agent should identify old/new behavior, affected extension points, transitional settings, warning conditions, and the future removal step. Ask whether users can migrate coherently and whether maintainers can follow removal comments without discovering unexplained failures. Historical warning class names are context, not instructions to copy into today's patch.

### Documentation should describe precise behavior in the right place

**High confidence.** In the CSP review she requested version metadata, clearer release prose, links to canonical settings documentation instead of duplicating it, and wording that names the report-only setting explicitly. In the File truthiness review she corrected an incomplete account of which built-in subclasses retained prior behavior. Recent archive-hardening comments request version markers on both command options. See E8, E9, and E12.

Look for misleading scope, unstated defaults, incorrect exception or return types, missing version markers, and duplicated reference material that could diverge. Do not translate this into a universal preference for shorter documentation: in CSP middleware docs she also suggested explicitly naming both headers the middleware sets.

### Keep the patch focused and classify release information correctly

**High confidence for scope; context-dependent for release classification.** She requested reverting unrelated changes in admin tests and an unrelated exception message in debug-test helpers. In mail work she asked to separate a Python-bug workaround and another bug fix from the API migration. She questioned unnecessary bug-fix release notes, while asking for backward-incompatibility information for GDAL and archive hardening. See E1, E3, E6, E7, E10, and E12.

Identify unrelated edits that increase review effort or obscure a behavior change. Explain the connection before recommending removal. Classify release notes using current project rules and the actual compatibility impact; do not use an old comment about bug-fix notes as a blanket prohibition on all release notes.

## Communication and judgment

The sample repeatedly pairs concrete code suggestions with a reason: reduce logging noise, prove cache retrieval, expose a missing path, preserve compatibility, or reduce documentation duplication. Suggestions are often provisional and leave space for a better implementation. In the Redis discussion she acknowledged unfamiliarity, linked the original implementation, sought experienced contributors, and asked why a pool reset was needed. That supports an agent practice of stating uncertainty and consulting context, rather than a claim about her temperament. See E1, E5, and E11.

Suggestions are not final authority. In E7, an edit she considered unrelated formatting was explained by the author as a necessary tuple conversion to evaluate a lazy value. In E5, Florian Apolloner later objected that the proposed cache marker header leaked to clients. Preserve these counterexamples: inspect replies, verify mechanisms, and reconsider an apparent nit when it has a functional purpose. Do not copy the header design or treat the first review comment as an accepted resolution.

## Evidence ledger

Each link below is a Sarah-authored inline comment unless another author is explicitly named. Titles identify the PR; dates identify the cited feedback.

| ID | PR and review date | Representative evidence |
| --- | --- | --- |
| E1 | **#19702 — Enhanced `ExceptionReporter` to handle GET and FILES parameters**; 2025-08-06 and 2025-08-11 | [Revert without failures](https://github.com/django/django/pull/19702#discussion_r2256999290); [missing files case exposes POST failure](https://github.com/django/django/pull/19702#discussion_r2265941050); [remove wrapper in favor of existing context](https://github.com/django/django/pull/19702#discussion_r2256961999); [capture test logging](https://github.com/django/django/pull/19702#discussion_r2256959023). |
| E2 | **#19191 — Added support for custom expiry in signed cookies**; 2025-02-25 | [Async test needed](https://github.com/django/django/pull/19191#discussion_r1969617834); [keep formatting consistent for review](https://github.com/django/django/pull/19191#discussion_r1969606080). |
| E3 | **#18966 — Updated django.core.mail to Python's modern email API**; 2025-06-18 and 2025-06-23 | [Change without test failures](https://github.com/django/django/pull/18966#discussion_r2154034460); [removal instructions fail tests](https://github.com/django/django/pull/18966#discussion_r2154068393); [documented return-type break](https://github.com/django/django/pull/18966#discussion_r2161383176); [separate workaround](https://github.com/django/django/pull/18966#discussion_r2161357496). |
| E4 | **#18805 — Allowed multiline template tags**; 2025-03-19 and 2025-04-16 | [Warning test and deprecation timeline](https://github.com/django/django/pull/18805#discussion_r2003564733); [warning opt-out question](https://github.com/django/django/pull/18805#discussion_r2003593745); [removal instructions including tests](https://github.com/django/django/pull/18805#discussion_r2046320805). |
| E5 | **#18297 — Prevented duplicate caching when using CacheMiddleware and cache_page**; 2024-07-08 | [Prove retrieval from cache](https://github.com/django/django/pull/18297#discussion_r1668299825). Counterexample: [Florian's objection to client-visible header](https://github.com/django/django/pull/18297#discussion_r1730127914), 2024-08-24. |
| E6 | **#19840 — Subclasses of BaseCache should use specialized implementation of methods for async version**; 2025-09-19 and 2025-09-25 | [Other overridden methods](https://github.com/django/django/pull/19840#discussion_r2362596749); [regression for non-overridden async implementations](https://github.com/django/django/pull/19840#discussion_r2378884073). |
| E7 | **#19529 — Refactored GDAL functions to be lazily loaded**; 2025-06-20 | [Compatibility precedent and usage search](https://github.com/django/django/pull/19529#discussion_r2158896601); [actual exception on main](https://github.com/django/django/pull/19529#discussion_r2158930559). Context: [author explains necessary tuple conversion](https://github.com/django/django/pull/19529#discussion_r2169304076), 2025-06-26. |
| E8 | **#19393 — Added Content-Security-Policy support**; 2025-06-26 | [Canonical settings documentation](https://github.com/django/django/pull/19393#discussion_r2168459155); [specify report-only behavior](https://github.com/django/django/pull/19393#discussion_r2168472272); [middleware header details](https://github.com/django/django/pull/19393#discussion_r2168446182). |
| E9 | **#20327 — Made base File objects truthy by default**; 2026-04-03 | [Precise subclass exceptions](https://github.com/django/django/pull/20327#discussion_r3032531267). |
| E10 | **#18934 — Rendered named choice groups with `<optgroup>` in FilteredSelectMultiple**; 2025-04-17 | [Revert unrelated changes](https://github.com/django/django/pull/18934#discussion_r2048692595). |
| E11 | **#18459 — Reusing RedisCacheClient Connections for multiple requests**; 2024-08-09 | [Acknowledge unfamiliarity and find prior work](https://github.com/django/django/pull/18459#discussion_r1711379372); [ask why pools reset](https://github.com/django/django/pull/18459#discussion_r1711393921). |
| E12 | **#21843 — Harden django.utils.archive against decompression bombs**; 2026-10-09 | [Both commands and simpler assertions](https://github.com/django/django/pull/21843#discussion_r4230387463); [version marker](https://github.com/django/django/pull/21843#discussion_r4230423330); [backward-incompatibility section](https://github.com/django/django/pull/21843#discussion_r4230290255). |

## Reusable review-agent instructions

Review the supplied patch through this independent lens. Read the ticket, intended behavior, current contribution guidance, nearby implementation, and relevant tests before proposing findings. Do not impersonate Sarah, copy her phrasing, predict approval, or present this document as project policy. Keep all output for the human author's private preparation or own fork.

1. Explain which observable behavior each important new test proves. Identify a missing case only when the changed mechanism supports it; offer a focused failing example where feasible.
2. Trace counterpart interfaces and inheritance. Check that a subclass fix preserves specialized base behavior and that async counterparts retain the promised semantics.
3. Audit compatibility, warnings, transitional options, and removal instructions together. Distinguish a deprecated path from an immediate behavior change.
4. Check documentation against actual defaults, exceptions, affected subclasses, and settings. Use canonical references and correct version metadata.
5. Flag unrelated edits with reasons. Check release-note placement against current rules, avoiding blanket historical rules.
6. Reconsider suggestions when author context supplies a functional justification. Record uncertainty and the information needed to resolve it.

**Output contract:** List actionable findings first, ordered by impact. Each must include a changed file and line, concrete trigger, failure mechanism, user or maintainer consequence, evidence, confidence, and a focused fix or test. Distinguish demonstrated defects from unresolved questions. Put optional clarity, formatting, or simplification suggestions in a separate short section, and omit them when they add little value. Report no findings if the evidence supports none; never invent issues to satisfy a persona. Summarize what was inspected and what remains unverified. The human author must verify every finding and proposed change before submission.
