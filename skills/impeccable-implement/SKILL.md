---
name: impeccable-implement
description: "Implement an Impeccable ticket from its issue URL, running the ticket's own agent prompt under the implement discipline."
disable-model-invocation: true
license: MIT
---

# Impeccable implement

The chain is `impeccable audit|critique` → `impeccable-to-tickets` → **here**.

An Impeccable ticket already carries its own brief: a label naming the command that fixes it, acceptance criteria written as observable outcomes, and a fenced agent prompt. This skill reads that ticket, loads Impeccable itself, and runs the prompt under `implement`'s discipline. The user pastes a URL, nothing else.

Argument: one or more issue URLs or `#numbers`. With several, run them one at a time in dependency order, each through the whole sequence below.

## 1. Intake

```bash
gh issue view <url> --json number,title,body,state,labels,assignees,url
```

Print an `Intake` block before anything else, carrying:

- **Command**: the `impeccable:<command>` label. `impeccable:audit` and `impeccable:critique` name the source of the finding, not the fix; the fix label is the other one.
- **Parent spec**: the `Parent: #N` line or the native sub-issue link.
- **Brief**: the fenced block under `## Agent prompt`, quoted verbatim into the Intake block so it stays in context for the whole run. Its first line is a command invocation (`/impeccable harden the contact form's failure path`): read it as the command plus the target, and do not dispatch it as a nested skill call. Step 4 already loads what it would load. When that command disagrees with the `impeccable:<command>` label, the label is the ticket's contract; report the mismatch and ask.
- **Criteria**: every checkbox under `## Acceptance criteria`.
- **Gates**: the tracker's own dependency edges, read from the tracker and not from the body.

  ```bash
  gh api repos/<owner>/<repo>/issues/<number> --jq .issue_dependencies_summary
  gh api repos/<owner>/<repo>/issues/<number>/dependencies/blocked_by --jq '[.[] | {number, state, title}]'
  ```

  `blocked_by` counts open blockers only, so it is the live gate. `total_blocked_by` counts every edge ever wired, open or closed. The `## Blocked by` prose is a mirror of those edges, written once when the ticket was created and never updated since: read it for the clause saying what each gate was for, never for whether the gate still holds.

  One case inverts that. When `total_blocked_by` is `0` and the body still lists blockers, this tracker has no edges to mirror and the prose is all there is: resolve every line it names with `gh issue view` and take their states as the gate.

When the ticket's measurement is thin, read the archived report the parent spec links, under `.impeccable/<audit|critique>/`. Never re-run the audit to fill a gap: an unmeasured finding is not in scope.

## 2. Stop conditions

Check these before claiming anything. On any hit, report it and stop.

- `impeccable:spec` label. A tracking issue is not a task. List its open children and ask which one.
- State is closed, or the assignee is somebody other than the authenticated user.
- A dependency edge to an open blocker. Name it by number and title and stop.

  Nothing in the `## Blocked by` prose stops a run by itself. A line naming a closed ticket is a stale mirror. A line naming no ticket at all, `BLOCKER_REQUIRED`, `TBD`, a bare `#NN`, is a placeholder the ticket writer left behind, and the edge it stands for is usually wired already and often closed already. Read the edges, say what they were in the Intake, and carry on. Asking the user to adjudicate a placeholder the tracker can resolve in one call is a round trip you owed them an answer on.

  A blocker you implemented earlier in this same run is a third case: still open in the tracker, because this skill never closes anything, but satisfied on disk, because its diff is sitting uncommitted in the working tree. Name it as landed-not-shipped and continue.
- `impeccable:shape`. Shape plans, it does not ship. Produce the plan, hand it back, make no production edit.
- No `## Agent prompt` block or no `## Acceptance criteria`. Wrong ticket shape; ask before proceeding.

## 3. Claim, then reconcile the mirror

`gh issue edit <number> --add-assignee @me`, then verify the authenticated user appears in the assignees. This is the session's first write.

Then bring the `## Blocked by` prose into line with the edges you just read, so the next reader is not sent to ask a question the tracker already answers:

- A blocker that is closed reads as landed, keeping its number, its title, and the clause saying what the gate was.
- A placeholder that an edge accounts for is replaced by the ticket that edge names.
- A placeholder no edge accounts for stays on the page, marked unfilled. It is the one thing in that section worth a human's attention, and deleting it is how it stops being visible.

Rewrite that section and nothing else, with `gh issue edit <number> --body-file -`. The criteria, the agent prompt, and the measurements are the contract; they survive this edit untouched.

## 4. Load Impeccable

Before drafting the acceptance map, so the design truth shapes the map rather than arriving after it.

Locate the skill, project copy first:

```bash
for d in .pi/skills/impeccable .claude/skills/impeccable ~/.pi/agent/skills/impeccable ~/.claude/skills/impeccable ~/.agents/skills/impeccable; do
  [ -d "$d" ] && echo "$d" && break
done
```

Then, with cwd at the project:

1. Run `node <impeccable-dir>/scripts/context.mjs --target <the file or route the ticket names>` once. Follow its directives and do not rerun it. A `CONTEXT_STALE` finding is reported to the user, not repaired as a side effect of this ticket.
2. Read `<impeccable-dir>/SKILL.md` and `<impeccable-dir>/reference/<command>.md`, taking the native variant on native platforms.
3. Read `<impeccable-dir>/reference/craft-floor.md` immediately before the first UI edit.

The command decides **how** the fix is made. The ticket decides **what** is in scope. Where the command reference's default scope is wider than the acceptance criteria, the criteria win.

## 5. Run the implement loop

Read [`../implement/SKILL.md`](../implement/SKILL.md) and follow it from **Acceptance Map** onward, with the ticket standing in for the spec and these substitutions:

- **The agent prompt is the brief**, and the only brief. Work its `file:line` measurements, its "what to change", and its named danger (live credentials, a real sender, production data) straight into the acceptance map. Every "keep this unchanged" clause is a constraint, not a suggestion.
- **One map row per checkbox**, no invented rows, no softened severity.
- **Proof is whatever the criterion names.** A criterion naming a repo command is proven by that command. A behavioral criterion goes red first, per `/tdd`. A visual criterion is proven by Impeccable's own inspection: one batched screenshot round at the viewports and themes the parent report used, plus the design detector over the target. Bounded passes, per Impeccable's core principles: build fully, inspect once, fix in one batch, confirm once, stop.
- **Unmeasured findings stay out of the diff.** Collect them for the parent spec and let the user decide.
- Freeze Gate and Final Verification run unchanged.

## 6. Hand back

Leave the changes uncommitted. Report:

- A criterion → evidence table, one row per checkbox.
- The ticket number and title, so the user's commit can carry `Closes #<number>`.
- **Unblocks**: every ticket this one blocks, by number and title, so the user can see what shipping this sets free.

  ```bash
  gh api repos/<owner>/<repo>/issues/<number>/dependencies/blocking --jq '[.[] | {number, state, title}]'
  ```

  Report them and leave them alone. This ticket is still open until the user ships, the edge clears itself the moment they do, and step 3 of the next run is what rewrites the prose on the other side.
- Anything noted for the parent spec.
- `Implementation Complete` on its own line.

Do not tick the issue's checkboxes, close it, or comment on it. The `## Blocked by` reconcile in step 3 is the only write this skill makes to a body. The review happens on the diff, and the user ships.
