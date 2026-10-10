# Mariusz Felisiak (`felixxm`): maintenance and backend extensibility

This is an independent synthesis of public reviews, for private patch preparation.
It is not Mariusz's own instructions, an endorsed persona, or a prediction of his
approval. Use the priorities below as questions to investigate, not as authority
for findings. Follow the project-wide human verification and AI disclosure
guidance in [README](README.md).

## Why include this reviewer?

As checked on October 10, 2026, Django's official [teams page](https://www.djangoproject.com/foundation/teams/)
lists Mariusz among Mergers, Releasers, and the triage and review team. The
[Fellowship history](https://www.djangoproject.com/fundraising/#who-are-the-django-fellows)
identifies him as a former Fellow, 2019–2024, with work on the ORM and Oracle
backend. These are documented responsibilities, rather than an inferred measure
of popularity. There is also direct community testimony: in a discussion about
`Excluded`, Sarah Boyce explained that his feedback had improved the cleanliness
of her designs and encouraged pursuing his suggestions. Her initial scope
reduction suggestion in that same comment was explicitly withdrawn; it is not
evidence for this profile's recommendations.
[Sarah's comment](https://github.com/django/django/pull/17515#discussion_r1442597457).

## Sample and limits

Research date: October 10, 2026. Discovery used GitHub issue search for
`repo:django/django is:pr commenter:felixxm`, sorted by recent update. Twelve
external-author PRs were screened. Nine contained substantive attributed review
comments used below; three contained only approval, no attributed comments, or a
single request about review timing, and were excluded from pattern evidence.
Comments used span August 2020 through October 2026. PR descriptions, inline
comment hunks, replies, review summaries, and relevant current file patches were
examined. An updated PR date is not treated as the date of Mariusz's feedback.

| PR | Context | Evidence used |
| --- | --- | --- |
| [12438](https://github.com/django/django/pull/12438) | Regular expression database functions | 2020 and 2022 reviews of backend hooks, semantics, and maintainability |
| [17515](https://github.com/django/django/pull/17515) | Conditional conflict updates in `bulk_create()` | December 2023 reviews of expression design, capability flags, tests, and async parity |
| [18241](https://github.com/django/django/pull/18241) | SQLite JSON extraction operator | July 2024 review of feature checks and benefit versus existing workaround |
| [19397](https://github.com/django/django/pull/19397) | Documentation heading guidelines | April 2025 review of examples and ticket completeness |
| [19930](https://github.com/django/django/pull/19930) | Oracle option deprecation | November 2025–January 2026 reviews of release targeting and removal instructions |
| [20007](https://github.com/django/django/pull/20007) | PostgreSQL test skip workaround | October 2025 review identifying the parallel test infrastructure issue |
| [21119](https://github.com/django/django/pull/21119) | Rename `savepoint()` | April 2026 review of deprecation documentation and warning attribution |
| [21663](https://github.com/django/django/pull/21663) | UUID function support in column defaults | July 2026 discussion adapting the capability model to setup behavior |
| [22112](https://github.com/django/django/pull/22112) | New Oracle driver compatibility | October 2026 comments on release wording and an additional regression test |

The selection deliberately includes several older discussions, but remains
database-heavy and is not a random or exhaustive sample. It supports a useful
maintenance lens, not a claim about everything he prioritizes. Open, superseded,
or closed PRs provide evidence of requests made; they do not establish that a
proposal became Django policy or shipped unchanged.

## Supported priorities

### Describe capabilities instead of assuming a closed list of backends

This recurs across separate proposals. In the regex review, Mariusz's main
objection was that the implementation could not readily accommodate third-party
backends; he proposed backend hooks and vendor methods rather than multiple
vendor checks in shared `as_sql()`. In the conflict-update expression, he asked
for a capability flag instead of hard-coded vendor names. In the SQLite JSON
operator patch, he requested a SQLite-specific feature flag for a version test.
[Regex review](https://github.com/django/django/pull/12438#pullrequestreview-490324708),
[conflict-update comment](https://github.com/django/django/pull/17515#discussion_r1431233070),
[SQLite comment](https://github.com/django/django/pull/18241#discussion_r1686920943).

For a review agent: examine whether shared code checks what a backend supports,
and whether an external backend can implement the extension point. A flag must
describe the actual operation. The UUID discussion distinguished function use in
queries from use in defaults after the author explained that model creation
occurred before an individual test could be skipped. Mariusz then supported a
separate flag. This is evidence of a context-dependent distinction, not a rule
that every backend discrepancy needs another flag.
[Author's setup explanation](https://github.com/django/django/pull/21663#issuecomment-5063918425),
[proposed distinction](https://github.com/django/django/pull/21663#issuecomment-5106084095),
[support for separate capabilities](https://github.com/django/django/pull/21663#issuecomment-5121533875).

### Put behavior in the smallest appropriate existing abstraction

Extensibility is balanced against unnecessary indirection. In `bulk_create()`,
Mariusz argued that generic expression compilation belonged in
`SQLInsertCompiler.as_sql()`, rather than a backend hook passing query, expression,
SQL, and parameters between layers. In regex functions, he proposed collecting
default flags in a backend operation to reduce duplicated vendor methods. These
point toward identifying the real variation before adding a hook, not toward an
absolute preference for more or fewer hooks.
[Compiler responsibility](https://github.com/django/django/pull/17515#discussion_r1431240731),
[default flags proposal](https://github.com/django/django/pull/12438#discussion_r829741719).

The latter proposal was challenged by the author because backend oddities might
still require vendor methods; this profile does not assume those methods could
all be eliminated. Mariusz also questioned the maintainability of the aggregate
flag handling in his review summary.
[Author's qualification](https://github.com/django/django/pull/12438#discussion_r830681155),
[maintainability concern](https://github.com/django/django/pull/12438#pullrequestreview-913998205).

His related requests to reuse `F` for `Excluded`, remove copied alias docstrings,
and revert unrelated formatting show concrete opportunities to reduce the amount
of new or duplicated code a maintainer must understand. The `Excluded` thread
included reported failures and further iteration; do not copy his exploratory
implementation as a proven solution.
[Reuse existing expression semantics](https://github.com/django/django/pull/17515#discussion_r1433798292),
[docstring duplication](https://github.com/django/django/pull/21119#discussion_r3117395514),
[unrelated formatting](https://github.com/django/django/pull/17515#discussion_r1434742217).

### Demand evidence that exercises the disputed behavior

The strongest test coverage example is one extended PR discussion, not many
independent instances of an identical rule. For `Excluded`, Mariusz asked for
coverage of the raised error and for a test that failed with his simpler proposal
but passed with the contributor's implementation. That turns a design claim into
a discriminating example.
[Error branch](https://github.com/django/django/pull/17515#discussion_r1433159757),
[distinguishing test](https://github.com/django/django/pull/17515#discussion_r1433192120).

Other PRs support the broader emphasis on relevant evidence. For regex cases he
asked whether a valuable test should run across databases, with the exceptional
Oracle behavior isolated in a separate test. For the PostgreSQL skip patch he
questioned what the new decorator changed, since the test was already supposed
to be skipped; he traced the issue to parallel execution with `spawn` and
`forkserver`. The author acknowledged that diagnosis and continued in the
alternative PR.
[Cross-backend case](https://github.com/django/django/pull/12438#discussion_r829744423),
[question about existing skip](https://github.com/django/django/pull/20007#discussion_r2468420831),
[underlying mechanism](https://github.com/django/django/pull/20007#discussion_r2468926193),
[author's response](https://github.com/django/django/pull/20007#discussion_r2469222842).

An important counterweight: in the recent Oracle driver PR he suggested removing
an elaborate extra test because existing GIS tests already crashed. The author
had identified its additional bytes-path coverage and invited a judgment on
whether it was worthwhile. This single example supports considering test value
and complexity together; it does not justify omitting regression coverage.
[Author's coverage rationale](https://github.com/django/django/pull/22112#discussion_r4223187147),
[Mariusz's response](https://github.com/django/django/pull/22112#discussion_r4237689830).

### Treat API changes as documentation and release maintenance work

Repeated reviews address exact version annotations, their placement, and release
timing. For `bulk_create()` he requested an updated signature and normal argument
documentation, with a short `versionchanged` note rather than the whole feature
inside that directive. For the savepoint rename he asked for the missing
`versionadded` and corrected the deprecation and removal descriptions. For the
Oracle deprecation he required retargeting to 6.1 and removal hints for releasers.
[Argument documentation](https://github.com/django/django/pull/17515#discussion_r1431230811),
[new function version](https://github.com/django/django/pull/21119#discussion_r3117384277),
[removal description](https://github.com/django/django/pull/21119#discussion_r3117391423),
[release target](https://github.com/django/django/pull/19930#discussion_r2547598170),
[removal hints](https://github.com/django/django/pull/19930#discussion_r2617407163).

Two narrower checks are useful without pretending they recur throughout the
sample: `abulk_create()` must expose the corresponding new functionality, and a
heading-guidelines patch should also fix the locations named in its ticket.
[Async parity](https://github.com/django/django/pull/17515#discussion_r1434869043),
[ticket completeness](https://github.com/django/django/pull/19397#pullrequestreview-2790778244).

### Prefer exact, useful prose over explanatory padding

Mariusz corrected copied documentation examples for fidelity to the source and
asked to remove the unsupported efficiency wording in a deprecation message. He
also required concise, actionable removal hints rather than verbose descriptions
of the nearby code. These examples support checking factual precision and purpose
before polishing tone.
[Accurate copied example](https://github.com/django/django/pull/19397#discussion_r2058188789),
[warning wording](https://github.com/django/django/pull/19930#discussion_r2620301464),
[removal hint refinement](https://github.com/django/django/pull/19930#discussion_r2620295408).

## Reusable private review instructions

You are an independent reviewer using the maintenance priorities documented
above. Do not speak as Mariusz, mimic his tone, infer his intentions, or predict
approval. Review only for private preparation or in the contributor's own fork;
the human author decides what to fix and verifies every result.

Read the current diff, ticket or stated problem, surrounding implementation,
relevant tests, and applicable contribution guidance. Use historical examples as
prompts to investigate the current patch. Check capability boundaries, extension
points, duplicated behavior, unnecessary indirection, demonstrable regressions,
public API counterparts, version annotations, warning attribution, and concise
documentation when those concerns apply. Avoid unrelated cleanup requests.

For each finding, provide:

1. The current patch file and line, and a concise description of the problem.
2. The concrete mechanism and affected case; distinguish a demonstrated defect
   from a design question or hypothesis.
3. Current code, documentation, or test evidence. Add the supporting historical
   comment only when it helps explain this lens; it cannot prove today's defect.
4. Severity based on actual consequence, plus the smallest focused correction or
   discriminating test. Do not demand an abstraction without identifying the
   variation it serves.

Separate optional wording or formatting nits from correctness findings. State
which checks ran, which did not, and any backend/version limitations. Do not
invent execution results or request tests that merely mirror implementation.
If no supported issue is found, say so. Produce a short report for human review,
and do not post an automated review to the upstream Django PR.
