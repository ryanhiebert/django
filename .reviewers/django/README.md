# Django review lenses

Research snapshot: **2026-10-10**. These are private preparation aids for the
patch author. Each profile combines a study of public `django/django` review
comments with instructions for an AI reviewer to examine similar concerns.

Prepared with Codex assistance for public-review research, profile drafting,
prompt writing, and source checking. These are AI-assisted research drafts for
Ryan to review and refine; source checking by an agent is not human verification.

The profiles are independent interpretations. They do not speak for the named
people, imply their endorsement, or predict their approval. Use their technical
priorities as review lenses; do not imitate their voice or present generated
feedback under their names. You remain responsible for understanding and
verifying every finding and every resulting change.

## Profiles and selection

| Person | GitHub account | Evidence for selection | Profile |
| --- | --- | --- | --- |
| Natalia Bidart | `nessita` | Current Fellow and Merger | [Natalia Bidart](natalia-bidart.md) |
| Sarah Boyce | `sarahboyce` | Current Fellow and Merger | [Sarah Boyce](sarah-boyce.md) |
| Jacob Walls | `jacobtylerwalls` | Current Fellow and Merger | [Jacob Walls](jacob-walls.md) |
| Mariusz Felisiak | `felixxm` | Current Merger; former Fellow, 2019–2024 | [Mariusz Felisiak](mariusz-felisiak.md) |
| Simon Charette | `charettes` | Current triage and review team; historical core team member | [Simon Charette](simon-charette.md) |
| Johannes Maron | `codingjoe` | Reviewed the command PR | [Johannes Maron](johannes-maron.md) |
| Benjamin Balder Bach | `benjaoming` | Reviewed the command PR | [Benjamin Balder Bach](benjamin-balder-bach.md) |
| Mike Edmunds | `medmunds` | Current triage and review team; reviewed the command PR | [Mike Edmunds](mike-edmunds.md) |
| Matthias Kestenholz | `matthiask` | Reviewed the predecessor command PR | [Matthias Kestenholz](matthias-kestenholz.md) |

Roles were checked against Django's [current team list](https://www.djangoproject.com/foundation/teams/),
[Fellowship history](https://www.djangoproject.com/fundraising/), and
[historical core team](https://www.djangoproject.com/foundation/django_core/).
Merger status is evidence of merge authority. Historical core team membership
does not establish present permissions. Community respect is not directly
measurable here; documented responsibility is the selection proxy.

As an initial discovery step, we read the two newest pages of the
[GitHub inline review comments endpoint](https://docs.github.com/en/rest/pulls/comments#list-review-comments-in-a-repository)
for `django/django`, ordered by creation time, 100 comments per page. The 200
comments spanned September 27–October 10, 2026 and 47 distinct PRs. The five
selected accounts authored 27, 16, 50, 3, and 11 of those comments respectively.
These counts include inline replies and potentially PR authors' own comments;
they are discovery signals, not counts of independent reviews or a ranking.
The pages were fetched successively while activity continued, so the sample is
an approximate recent window rather than an atomic repository snapshot.

Other active accounts in this small sample included `codingjoe` (31 comments),
`medmunds` (18), `timgraham` (4), and `carltongibson` (1). The initial set favors
the current Fellows plus experienced reviewers; it is not exhaustive or a claim
that unselected contributors are less valuable.

The scope was extended to include all external human accounts contributing
reviews, inline feedback, or review-related discussion on Ryan's command PR,
[#21260, “Fixed #36022 -- Renamed django-admin to django.”](https://github.com/django/django/pull/21260),
and its predecessor,
[#18987, “Fixed #36022 -- Added django alternative spelling of django-admin.”](https://github.com/django/django/pull/18987).
We read all paginated review, inline-comment, and issue-discussion endpoints for
both PRs. The current PR has formal review records from `codingjoe`, `benjaoming`,
`medmunds`, and `jacobtylerwalls`; the predecessor has a review from `matthiask`
and a contribution-planning discussion comment from `nessita`. All six are
covered. Ryan's own replies are excluded from this reviewer set. The linked
[fork PR #2](https://github.com/ryanhiebert/django/pull/2) had no formal reviews
at the time checked. Roles are not a prerequisite for this patch-specific set.

A separate research agent examined each selected person's feedback. Each
profile records its own larger, deliberately selected PR sample, dates,
comment links, recurring concerns, and limitations. The sample is qualitative:
it is useful for generating questions to check, not estimating how frequently a
person will raise an issue. All reviewers can notice concerns outside these
profiles, and many priorities overlap.

## Choosing a first pass

These are suggested starting points inferred from the samples, not exclusive
specialties. The profiles explain the supporting patterns and their limits;
the links here show representative receipts.

| Lens | Useful starting questions | Example receipts |
| --- | --- | --- |
| [Natalia](natalia-bidart.md) | Does cleanup match resource lifetime? Do docs preserve the actual contract? | [Cleanup](https://github.com/django/django/pull/19153#discussion_r1992324456), [lost documentation promise](https://github.com/django/django/pull/21764#discussion_r3997845740) |
| [Sarah](sarah-boyce.md) | Are counterpart interfaces covered? Are deprecation removal instructions executable? | [Async counterpart](https://github.com/django/django/pull/19191#discussion_r1969617834), [removal instructions](https://github.com/django/django/pull/18966#discussion_r2154068393) |
| [Jacob](jacob-walls.md) | Does a test distinguish the complete fix? Does cleaner placement preserve compatibility? | [Discriminating test](https://github.com/django/django/pull/21922#discussion_r4234281905), [historical migrations](https://github.com/django/django/pull/21890#discussion_r4146058717) |
| [Mariusz](mariusz-felisiak.md) | Does shared code express backend capabilities and permit extension? | [Backend hooks](https://github.com/django/django/pull/12438#pullrequestreview-490324708), [capability flag](https://github.com/django/django/pull/17515#discussion_r1431233070) |
| [Simon](simon-charette.md) | Does a shared-helper fix preserve neighboring semantic contracts and invariants? | [Deferred fields](https://github.com/django/django/pull/17583#discussion_r1418148690), [caller-owned input](https://github.com/django/django/pull/17417#issuecomment-1825785903) |
| [Johannes](johannes-maron.md) | Are concepts named clearly? Which negative case distinguishes correct behavior? | [CLI terminology](https://github.com/django/django/pull/21260#pullrequestreview-4265283992), [non-path imports](https://github.com/django/django/pull/20942#discussion_r3254293231) |
| [Benjamin](benjamin-balder-bach.md) | Does the reading path explain unfamiliar behavior without duplicating existing material? | [Explain lazy loading](https://github.com/django/django/pull/19529#discussion_r2358152661), [reuse an explanation](https://github.com/django/django/pull/18348#pullrequestreview-2201719680) |
| [Mike](mike-edmunds.md) | Will permanent docs retain the contract? Are third-party extension callers preserved? | [Permanent parameter docs](https://github.com/django/django/pull/21610#discussion_r3962851683), [subclass compatibility](https://github.com/django/django/pull/20469#discussion_r2823586558) |
| [Matthias](matthias-kestenholz.md) | Do tests prove observable behavior? Do examples identify who supplies the implementation? | [Language behavior](https://github.com/django/django/pull/10708#discussion_r254042887), [example ownership](https://github.com/django/django/pull/18277#discussion_r1770628270) |

## Use privately before submitting

Read the relevant profile and pass its agent instructions, the exact patch,
base revision, ticket or proposal, and nearby code/tests to a reviewer. Specify
the GitButler branch or exact diff: other applied branches may be visible in the
same working tree. Ask the agent to inspect the supplied patch without editing
files. A profile is a Markdown prompt, not an installed skill or an automatically
configured agent.

Use this shared instruction alongside the profile:

```text
Review the supplied patch through the technical lens in this profile. This is
an independent review; do not claim to be or speak for the named person.
Inspect the patch, relevant surrounding code, ticket, tests, and documentation.
Use historical comments as prompts for investigation, not binding rules.
Report only issues supported by the current patch and project requirements.
For each actionable finding, give the path and line, failure mechanism or
maintenance consequence, evidence, and a focused remedy. Explain severity.
Keep optional polish separate; do not inflate it into a correctness defect.
Mark uncertainty and unrun checks. An empty findings list is acceptable.
Do not edit files, request an upstream automated review, or post feedback.
Return findings to the patch author for human verification.
```

For multiple lenses, reconcile duplicate findings before changing the patch.
Do not treat an agreement between agents as independent proof: they can repeat
the same mistake. Reproduce alleged bugs, check tests against the unfixed
behavior where practical, and discard suggestions that do not improve this
patch. Rewrite accepted changes in your own understanding and words. Maintain
the applicable Django contribution checklist and AI disclosure.

Django's [contribution policy](https://docs.djangoproject.com/en/dev/internals/contributing/writing-code/submitting-patches/#ai-assisted-contributions)
requires disclosure and verification of AI assistance and prohibits requesting
automated AI reviews on upstream Django PRs. The
[PR template](https://github.com/django/django/blob/main/.github/pull_request_template.md)
explicitly permits doing so in your own fork. The same rules appear in this
checkout's `docs/internals/contributing/writing-code/submitting-patches.txt` and
`.github/pull_request_template.md`. These lenses support your preparation;
they do not replace the community's review or the project's requirements.

## Refreshing the research

Follow the cited threads rather than relying only on isolated sentences.
Historical comments may refer to code since changed, supported versions since
dropped, or constraints absent from your patch. When extending a profile, record
the sampling method, PRs, comment dates, and exact author-attributed links.
Support a recurring priority with independent PR examples, distinguish a
one-off observation from a repeated pattern, and revise confidence when new
evidence contradicts an interpretation. Recheck current roles and policy.

All files here are new, author-facing Markdown documents. They are outside
Django's Sphinx source tree and require no changes to code, tests, build
configuration, or existing documentation. Keep this research branch separate
from an upstream Django patch unless you intentionally decide otherwise.
