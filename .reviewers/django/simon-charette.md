# Simon Charette (`charettes`): semantic contracts and regression evidence

Research snapshot: **2026-10-10**. This is an independent interpretation of
public review feedback, intended for private patch preparation. It is not an
impersonation, an endorsement, or a prediction of Simon's response.

## Selection and evidence boundaries

Django's [current team list](https://www.djangoproject.com/foundation/teams/)
includes Simon in the triage and review team and the security team. The
[historical core team list](https://www.djangoproject.com/foundation/django_core/)
also includes him. That historical team dissolved in 2020; neither the old title
nor this research establishes current merge permissions. He is not listed among
current Fellows or Mergers on the team page checked for this snapshot.

The corpus contains **67 substantive, nonempty comment/review bodies on 13
other-author `django/django` PRs**, dated **2019-12-09 through 2026-09-29**.
These are 43 inline comments, 14 issue-discussion comments, and ten nonempty
review summaries; multiple messages from one PR are not independent evidence.
Discovery used GitHub issue search with `repo:django/django is:pr
commenter:charettes`, sorted by recent update, and a separate older search with
`updated:<2024-01-01`. Samples were deliberately selected for varied subjects,
then read through their PR descriptions, changed files, review bodies, inline
diff hunks, and available replies. This is qualitative sampling, not a frequency
estimate or an exhaustive account of his work.

The strongest evidence concerns ORM and database changes. Forms and signing
provide additional contexts, but this sample cannot establish an equivalent
specialism in management commands, accessibility, or every Django subsystem.
The `django` command PR should use transferable review questions below; ORM
examples are not proof that its implementation needs database-specific checks.

## Recurring priorities

### Preserve the behavior of neighboring cases, not just the reported example

**High confidence, repeated across independent PRs.** A local fix can alter a
shared contract used elsewhere. In [#17583, explicitly deferred generated
fields](https://github.com/django/django/pull/17583#discussion_r1418148690), he
identified a case excluded by a proposed change to `get_deferred_fields()`, then
explained its consequence for
[refreshing deferred values](https://github.com/django/django/pull/17583#discussion_r1418188239).
His proposed boundary was to specialize the behavior for
[`Model.save()`](https://github.com/django/django/pull/17583#discussion_r1418194999).
In [#17417, a reused `fields` list](https://github.com/django/django/pull/17417#issuecomment-1825785903),
he showed that fixing iteration still left caller-owned input mutated, affecting
a later refresh. In [#21625, property setter storage](https://github.com/django/django/pull/21625#discussion_r3580535426),
he challenged an assumption that arbitrary setters cache under a predictable
attribute; their internals are opaque to the ORM.

Review implication: trace consumers of a changed helper and test repeated use,
explicitly configured behavior, and extension points. Do not generalize from
one implementation's storage or lifecycle to all objects sharing its interface.

### Regression tests must protect the underlying invariant

**High confidence, repeated.** In [#21921's review](https://github.com/django/django/pull/21921#pullrequestreview-5135696058),
he questioned changed regression expectations and requested a concrete cyclic
relationship case: deleting models must retain operations needed to break the
cycle. The author's [reply](https://github.com/django/django/pull/21921#issuecomment-5581838070)
reported reproducing that failure. In
[#22040](https://github.com/django/django/pull/22040#discussion_r4111561752), he
asked to restore an existing expectation and iterate on the failing
implementation. Conversely, [#17539](https://github.com/django/django/pull/17539#pullrequestreview-1776597456)
distinguished already-covered additions from alterations needing an assertion
about the resulting stored value.

The interpretation is not that existing assertions are immutable. It is that
the reviewer must establish why the promised behavior changes, rather than
accepting a new output merely because the implementation produces it. Review
tests against the ticket's invariant and real effects, including migration
executability and resulting data where relevant.

### Keep test coverage focused and tolerant of valid implementations

**High confidence, repeated.** In [#17567](https://github.com/django/django/pull/17567#pullrequestreview-1760487178),
he identified redundant coverage and suggested names distinguishing an index
from a constraint. In [#21605's admin test](https://github.com/django/django/pull/21605#discussion_r3551231928),
he questioned dependence on a particular SQL form and proposed comparing
against an expected queryset. In [#21714](https://github.com/django/django/pull/21714#discussion_r3704177372),
he suggested removing a mock of a normalization helper.

Review implication: add tests for new failure modes, use existing coverage
where it already proves the same condition, and avoid treating implementation
spelling or helper-call counts as the public contract. SQL-level assertions can
still be appropriate when SQL behavior is what the regression concerns.

### Respect abstraction boundaries and reuse existing machinery

**High confidence, repeated.** In
[#11783](https://github.com/django/django/pull/11783#discussion_r541011288), he
recommended the compiler's existing dispatch instead of manual vendor dispatch;
in a [separate comment](https://github.com/django/django/pull/11783#discussion_r541013300),
he questioned putting parameter preparation inside generic `execute()` rather
than its callers. In [#21605](https://github.com/django/django/pull/21605#discussion_r3551131638),
he requested extracting duplicated lookup logic. In
[#21651](https://github.com/django/django/pull/21651#issuecomment-5036069642),
he weighed whether third-party serializer transitions belong within Django's
maintenance responsibility.

Review implication: ask which layer owns a guarantee, which existing mechanism
already implements it, and what future obligations a seemingly small change
creates. The serializer case is a scoped objection to that proposed contract,
not evidence of blanket opposition to third-party interoperability.

### Check database semantics and supported variants explicitly

**High confidence, repeated.** In [#17539](https://github.com/django/django/pull/17539#issuecomment-1830250257),
he asked about MySQL strict mode and the values populated after a column
addition. His [follow-up](https://github.com/django/django/pull/17539#issuecomment-1830268670)
reported local confirmation and identified JSON's different implicit default.
In [#22040](https://github.com/django/django/pull/22040#discussion_r4133111151),
he explained the interaction between SQL NULL semantics, Django's equality
abstraction, and outer joins. His
[expectation table](https://github.com/django/django/pull/22040#issuecomment-5854209291)
compared positive and negated lookups with and without `None`.

Review implication: specify the expected behavior before assessing the SQL
rewrite. Check related type families and both positive and negative paths;
feature flags and superficially similar backend types do not prove semantic
equivalence.

### Demand evidence for optimizations and check the common path

**Moderate confidence.** In [#21714](https://github.com/django/django/pull/21714#discussion_r3704156516),
he argued that avoiding one helper call could add work for model-instance
inputs. The [review summary](https://github.com/django/django/pull/21714#pullrequestreview-4844360879)
requested a performance regression benchmark, and a
[later comment](https://github.com/django/django/pull/21714#issuecomment-5372929575)
asked for the benchmark to land and run before merging. Independently,
[#17539](https://github.com/django/django/pull/17539#issuecomment-1844067582)
traced two alteration paths before accepting a schema optimization.

Benchmark infrastructure is directly evidenced in one PR, so it should not
be portrayed as a universal requirement. The broader repeated concern is that
an optimization must preserve relevant paths and have evidence appropriate to
its claim.

### Make user-facing explanations accurate enough to guide action

**Moderate confidence, repeated.** In
[#21776](https://github.com/django/django/pull/21776#discussion_r3777179373),
he requested app-qualified model names because different apps can share an
object name. In [#17583's release note](https://github.com/django/django/pull/17583#discussion_r1418949948),
he asked to mention `GeneratedField` as part of the crash's conditions. In
[#11783's review](https://github.com/django/django/pull/11783#pullrequestreview-328658326),
he wanted documentation to distinguish expression defaults from database DDL
defaults.

Review implication: diagnostics should identify the actual conflicting objects,
and documentation should state the precise trigger and promise. This is
evidence for technical clarity, not a comprehensive prose style guide.

## How feedback is presented; limited observations

In this sample, technical explanations sometimes include concrete counterexamples:
the [reused input list](https://github.com/django/django/pull/17417#issuecomment-1825785903)
and [deferred-field refresh](https://github.com/django/django/pull/17583#discussion_r1418188239)
make consequences reproducible. The
[MySQL follow-up](https://github.com/django/django/pull/17539#issuecomment-1830268670)
also records changing the assessment after local verification. These support
using mechanism and reproduction in feedback, not copying his phrasing.

Two scoped observations should remain separate from recurring findings:

- In [#21605](https://github.com/django/django/pull/21605#discussion_r3552320942),
  he explicitly questioned whether the author had written and tried the code
  locally after an interaction involving an agent. This supports the practical
  importance of author understanding in that exchange; it does not establish
  his position on every use of AI.
- In [#21411](https://github.com/django/django/pull/21411#pullrequestreview-4414893594),
  he considered a small disabled-JSONField fix and its tests ready for review.
  A concise positive review cannot establish a detailed priority by itself.

## Reusable review-agent instructions — inferred from the evidence

These are our proposed instructions, not Simon's instructions. Use alongside
the shared policy and output requirements in [README.md](README.md).

```text
Review the supplied branch diff without editing or posting anything. You are
an independent reviewer applying a lens informed by Simon Charette's public
feedback; never claim to be him, use his voice, or predict his approval.

Read the ticket/proposal, surrounding implementation, tests, and documentation.
Identify the promised behavior. Trace changes to shared helpers and their
callers, input ownership, repeated calls, and supported extension points.
Challenge assumptions about internals that an interface does not guarantee.

For changed test expectations, explain whether the underlying guarantee has
changed intentionally. Seek a concrete counterexample before alleging a
regression. Prefer tests of observable invariants; distinguish essential new
coverage from redundant or implementation-bound assertions.

Check whether a change belongs at this layer and whether existing dispatch or
preparation machinery can implement it consistently. Treat new maintenance
obligations as tradeoffs requiring evidence, not automatic defects.

For database changes, inspect NULL/negation/join behavior, relevant field
families, backend features, schema operations, and existing stored data. For
performance claims, examine representative inputs and appropriate measurements.
For management commands, transfer the same contract-focused approach to the
documented entry points, caller interactions, and diagnostics; do not invent
database checks unrelated to the patch.

For each actionable finding, give the current patch path and line, trigger,
failure mechanism or maintenance consequence, patch/project evidence,
explained severity, and smallest focused remedy. Historical references guide
investigation and are not proof of a defect in the current patch. Put optional
nits separately. Mark uncertainty and unrun checks; empty findings are fine.
Return the review privately to the author for human verification and refinement.
Do not request an automated review or post feedback on upstream Django PRs.
```

## Corpus for checking and refreshing this profile

The linked anchor in each row is an author-attributed evidence item, not a
claim that the entire PR expresses only that priority. Open threads and final
diffs can change; use the inline comment's historical hunk when reconstructing
the proposed code at review time.

| PR | Context | Simon's nonempty bodies | Example evidence |
| --- | --- | ---: | --- |
| #11783 | Expression defaults | 6 | [Compiler reuse](https://github.com/django/django/pull/11783#discussion_r541011288) |
| #17417 | Input mutation on refresh | 1 | [Reused-list example](https://github.com/django/django/pull/17417#issuecomment-1825785903) |
| #17539 | MySQL schema defaults | 6 | [Stored-value assertions](https://github.com/django/django/pull/17539#pullrequestreview-1776597456) |
| #17567 | Null-distinct constraints | 6 | [Coverage and naming](https://github.com/django/django/pull/17567#pullrequestreview-1760487178) |
| #17583 | Generated and deferred fields | 5 | [Affected refresh behavior](https://github.com/django/django/pull/17583#discussion_r1418188239) |
| #21411 | Disabled JSON form fields | 2 | [Scoped approval](https://github.com/django/django/pull/21411#pullrequestreview-4414893594) |
| #21605 | PostgreSQL array lookup optimization | 19 | [Change decomposition](https://github.com/django/django/pull/21605#pullrequestreview-4662423583) |
| #21625 | Prefetch property setters | 2 | [Opaque bookkeeping](https://github.com/django/django/pull/21625#discussion_r3580535426) |
| #21651 | Signing serializer encoding | 5 | [Maintenance responsibility](https://github.com/django/django/pull/21651#issuecomment-5036069642) |
| #21714 | Related lookup preparation | 5 | [Benchmark request](https://github.com/django/django/pull/21714#pullrequestreview-4844360879) |
| #21776 | Unrelated prefetch querysets | 1 | [Qualified model names](https://github.com/django/django/pull/21776#discussion_r3777179373) |
| #21921 | Migration reduction | 3 | [Preserve cycle handling](https://github.com/django/django/pull/21921#pullrequestreview-5135696058) |
| #22040 | NULL lookup consistency | 6 | [Behavior table](https://github.com/django/django/pull/22040#issuecomment-5854209291) |

The corpus and interpretation omit private reviews, undocumented intentions,
and any claim about personality. Confidence here means support within this
selected sample, not how often a concern appears or how likely it is to block
the next patch. Recheck linked comments, current code, supported versions, and
Django's contribution policy before relying on the profile.
