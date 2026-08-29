---
name: implement
description: "Implement a piece of work based on a spec or set of tickets."
disable-model-invocation: true
---

Implement the work described by the user in the spec or tickets.

## Readiness

Before changing code, claim every open ticket being implemented using the project's issue tracker.
For GitHub issues, run `gh issue edit <number> --add-assignee @me`, then verify the authenticated
user appears in the issue's assignees.

Treat an approved spec or an implementation-ready ticket with clear acceptance criteria as an
approved design. Ask one clarifying question only when a material ambiguity blocks safe execution.
If the work still needs product or architectural decisions, stop and resolve them first: use the
`grilling` skill to pressure-test a specific open decision, or `wayfinder` when the work is too big
to hold in one session and the route is still unclear.

## Acceptance Map

Before the first production edit, draft a compact map with one row per acceptance criterion:
`criterion | likely production seam | test or inspection`.

Run one batched reconnaissance pass covering the issue, repository rules, relevant scripts, and the
candidate files required to validate the map. Finalize each row with the narrowest seam that can
prove it. One targeted follow-up is allowed for a concrete unknown. Reconnaissance is complete when
the first red test can be written; otherwise ask the single material question that blocks it.

Before any production edit call, print the finalized table under `Acceptance Map Ready`. This visible
checkpoint is the completion criterion for reconnaissance; implementation cannot begin from an
implicit or incomplete map.

## Seam Loop

Complete one mapped seam at a time:

1. Use /tdd where possible and prove the behavior red.
2. Make one coherent production edit batch that addresses the red behavior.
3. Run the affected single test file until the seam is green.
4. Refactor while green, then mark the map row complete.

Read a target file once per seam and use exact slices for follow-ups. Combine non-overlapping changes
to the same file into one edit call. After an exact-match edit failure, reread only the target region
and issue one corrected edit. A third production edit batch to the same file means the seam needs to
be replanned before continuing.

A newly discovered subsystem or behavior first becomes a row in the acceptance map. Continue only
when it is required by an existing criterion; otherwise leave it out of scope.

## Freeze Gate

Final verification begins only when:

- every acceptance-map row has green focused proof or a completed inspection;
- no material design or correctness question remains open;
- the working-tree diff contains only mapped work; and
- /code-review reports no unresolved material finding, or an equivalent standards/spec inspection is
  complete when no Git fixed point exists.

Before final verification, print `Freeze Gate Passed` with evidence for every condition above. Any
subsequent production edit invalidates that checkpoint and reopens the relevant seam. Fix the seam,
rerun only its focused proof, repeat /code-review when the diff materially changed, and pass the
Freeze Gate again.

## Final Verification

After the Freeze Gate passes:

1. Run formatting, linting, typechecking, and affected focused tests in one quality-gate batch.
2. Run the full test suite once, and only while the latest visible checkpoint is `Freeze Gate Passed`.
   The production diff is frozen once this run begins.
3. For unexpected failures, immediately run only the failing files against clean `HEAD`. Report
   identical failures as baseline. A new regression reopens the Seam Loop and requires a replacement
   Freeze Gate before another final run.

The work is complete when every acceptance-map row is accounted for, the Freeze Gate passes, and the
final full-suite result is reported. End a successful handoff with `Implementation Complete` on its
own line so the implementation gate resets. If the workflow is abandoned, the user can run
`/implementation-gate-reset`.

Leave the changes uncommitted and hand them back to the user for review. Commit only after the
user explicitly requests a commit.
