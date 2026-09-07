---
name: impeccable-to-tickets
description: Turn an Impeccable audit or critique into a spec issue on the tracker plus one sub-issue per fix, each labelled with the Impeccable command that fixes it and ending in a copy-paste agent prompt. Use after /impeccable audit or /impeccable critique when the findings should become tracked work.
disable-model-invocation: true
license: MIT
---

# Impeccable to tickets

`/impeccable audit` and `/impeccable critique` end with a list of recommended commands. This skill turns that list into tracked work: one **spec** issue that holds the evidence and the decisions, and one **sub-issue** per fix that an agent can pick up cold.

The chain is `impeccable audit|critique` → **spec** → **tickets**. This skill owns the last two steps.

It is the Impeccable-shaped sibling of `to-spec` and `to-tickets`. Where those slice a *plan* into tracer bullets, this one slices a *finding list* into fixes, and every fix already knows which command repairs it.

## Preconditions

1. **A finished audit or critique.** Either still in this conversation, or a path or issue reference the user passes as an argument. If neither exists, stop and tell the user to run `/impeccable audit <target>` or `/impeccable critique <target>` first. Never invent findings, and never re-run the audit yourself to fill a gap; a ticket with no measurement behind it is the thing this skill exists to prevent.
2. **A tracker.** Read the issue-tracker conventions the environment provides. Where the tracker is GitHub, the exact commands are in [reference/github.md](reference/github.md). Adapt the shape, never the content, for other trackers.
3. **Product truth.** Read `PRODUCT.md` and `DESIGN.md` if they exist. Their rules are what make a ticket's acceptance criteria binding instead of advisory.

## Process

### 1. Archive the report

If the report is not already on disk, write it to `.impeccable/<command>/<ISO-timestamp>__<target-slug>.md` (`command` is `audit` or `critique`). Match the frontmatter of any sibling already in that folder. The spec links to this path, so the evidence survives the conversation that produced it.

Timestamp: `date -u +"%Y-%m-%dT%H-%M-%SZ"`. Slug: the target path with separators and dots collapsed to hyphens.

### 2. Group the findings into tickets

Start from the report's **Recommended actions**, not from its finding list. Each recommendation names one Impeccable command. Then correct the grouping:

<grouping-rules>

- **One command per ticket.** A ticket labelled `impeccable:harden` is fixed by running `/impeccable harden`. Two commands means two tickets.
- **Split a bundled command by user journey.** An audit routinely puts five findings under one `harden`. Ask what a single visitor experiences: "what happens after I press Send" is one ticket; "the endpoint has no rate limit" is another. If the fixes would land in one commit and share acceptance criteria, keep them together.
- **Group trailing P3s by surface, not by severity.** Three small defects in one component are one ticket. Three small defects in three unrelated files are three, or a `polish` ticket per surface.
- **Never split a finding across tickets.** Its measurement, its fix, and its test live together.
- **Every ticket is verifiable alone.** If closing it depends on another ticket's outcome to know whether it worked, they are one ticket.
- **Carry severity into the title** as `[P0]`–`[P3]`, matching the report.

</grouping-rules>

Then set **blocking edges**, and only real ones. Two tickets touching the same file is a merge conflict, not a dependency. A real gate is one of:

- The blocker changes the structure the blocked ticket polishes. Polish always comes last on a surface being restructured.
- The blocker decides a value the blocked ticket consumes (corrected copy, a token, a resolved id).
- The blocker fixes wiring the blocked ticket builds on.

### 3. Confirm the breakdown

Present the grouping as a table: title, command label, severity, blocked by, and the one-line outcome. Ask whether the granularity is right and whether the edges are real. Iterate until approved.

Skip this step only when the user's invocation already said to publish (they asked for tickets, not for a plan for tickets).

### 4. Publish the spec issue

One issue, the parent. It is a **tracking issue, not a task**: no `ready-for-agent` label, no acceptance checkboxes, nobody assigned. Open the body with a line that says so.

<spec-template>

> Tracking issue. No work happens here. Every change lives in a sub-issue below.

## Context

Which command ran, against which target, on what date. The archived report path. The score, and the movement since the last run on this surface if there is one.

Any disclosure the run owes the user (a live endpoint that was hit, data that was written, mail that may have been sent) goes here, in bold, not buried in a sub-issue.

## Evidence base

What was actually measured: detector output, verify and typecheck results, the accessibility engine and its rule tags, viewports and themes, the instrumentation used for any performance claim, the interaction passes. Reproducibility is the point.

## Score table

The dimension or heuristic table from the report, verbatim.

## Problem statement

The two or three failures that matter, argued from measurements and from PRODUCT.md, in the user's language. Name the systemic pattern underneath them if there is one, including gaps in the test suite that let the defects ship.

Close with the honest summary, e.g. "The craft is not the problem. The last mile is."

## Solution

The shape of the fix in a short paragraph. Not a task list; the sub-issues are the task list.

## User stories

A long numbered list, `As a <actor>, I want <capability>, so that <benefit>`. Use the real actors from PRODUCT.md, not "a user". Cover every sub-issue and the reason it is worth doing.

## Implementation decisions

Decisions taken across the whole batch: what stays untouched, which shared module absorbs a fix, which approach was chosen over which alternative. Cite the design-system rules that bind. No file paths beyond the module level and no code, they go stale.

## Testing decisions

The seams the work is tested at, preferring seams that already exist, and the prior art in the repo for each. Name the test files that will change and why each is the right level.

## Out of scope

What this batch deliberately does not touch, including other surfaces with their own tracking issues.

## Sequencing

Which sub-issues are independent, and which are gated on which.

## Definition of done

All sub-issues closed, the repo's verification commands pass, the detector exits clean over the target, and the command is re-run with the score expected to move above its current value.

</spec-template>

### 5. Publish the sub-issues

One issue per ticket, in dependency order so blockers exist before the tickets that reference them. Each is a **real sub-issue of the spec**, created through the tracker's native parent relationship. A task list in the parent body, a comment on the parent, or a "Part of #N" line is not a sub-issue; use the native link and verify it afterwards.

<ticket-template>

An opening paragraph that states the defect as the visitor experiences it, in one sentence.

Then the measurement. Quote the numbers the audit actually produced, with the `file:line` where the cause lives. A table earns its place when there are three or more measured values. Quote PRODUCT.md or DESIGN.md where the rule being broken is written down.

Then, when it applies, the honest counterweight: what is already right in this code, so the agent picking it up does not "fix" something that was deliberate.

If a test should have caught it, say which test and which line lets it through. That gap becomes an acceptance criterion.

## Acceptance criteria

- [ ] Checkboxes covering the fix, the edge cases, the design-system rules that must survive, and the test that closes the gap.
- [ ] Written as observable outcomes, not implementation steps.
- [ ] The repo's own verification commands named explicitly.

## Agent prompt

```
/impeccable <command> <short target description>

The measurement, the file:line, and the cause, in plain sentences.

What to change, and what must not change.

Any constraint the agent would otherwise violate: design-system rules, product
rules about invented content, credentials that must not be reached by tests.

Where to cover it with a test.
```

## Blocked by

Issue references with a clause saying what the gate actually is, or "None (can start immediately)".

</ticket-template>

### 6. Wire the edges, then verify

Set the tracker's native blocking relationships to match every "Blocked by" line. Then read the tree back and confirm the sub-issue count, the labels, and the dependency counts. Report the tree to the user.

## The agent prompt block

The prompt is the reason these tickets are worth more than the report. Someone copies it into a fresh session with no memory of the audit.

<prompt-rules>

- **Open with the real command.** `/impeccable harden the contact form submit flow`. The command matches the ticket's label exactly.
- **Carry the measurement in.** "Focus goes to `<body>`, measured on both the success and the 502 path" tells a fresh agent what to reproduce. "Improve focus handling" does not.
- **Cite `file:line`.** The one place staleness is worth the risk, because it is what saves the reader twenty minutes.
- **State what must not change.** The design world, the product's content rules, the passing tests. An unconstrained agent restyles things nobody asked it to restyle.
- **Name the danger.** Live API keys in `.env.local`, a production database, a real mail sender: say it, and say to intercept.
- **Say "plan first, do not ship"** for any `shape` ticket, and for anything where the structure is still an open question.
- **Keep it inside a fenced block** so it survives copy-paste, and keep it under roughly 20 lines.

</prompt-rules>

## Labels

Every ticket carries `impeccable:<command>`, naming the command that fixes it. Create a label on demand if it does not exist; never silently drop one.

| Label | Colour | For |
|---|---|---|
| `impeccable:spec` | `0B3D5C` | The parent tracking issue only |
| `impeccable:audit` / `impeccable:critique` | `5A5A5A` | Which command found it. Every ticket in the batch carries one |
| `impeccable:shape` `impeccable:document` `impeccable:extract` | `6F42C1` | Build |
| `impeccable:polish` `impeccable:bolder` `impeccable:quieter` `impeccable:distill` `impeccable:harden` `impeccable:onboard` | `D93F0B` | Refine |
| `impeccable:animate` `impeccable:colorize` `impeccable:typeset` `impeccable:layout` `impeccable:delight` `impeccable:overdrive` | `006B75` | Enhance |
| `impeccable:clarify` `impeccable:adapt` `impeccable:optimize` | `FBCA04` | Fix |

Alongside those, apply the repo's existing vocabulary: its bug / enhancement / accessibility labels, and its agent-ready triage label on every sub-issue. The spec never gets the agent-ready label.

Only these commands may be used, matching what audit and critique are allowed to recommend: `adapt`, `animate`, `bolder`, `clarify`, `colorize`, `delight`, `distill`, `document`, `harden`, `layout`, `onboard`, `optimize`, `overdrive`, `polish`, `quieter`, `shape`, `typeset`. `audit` and `critique` label the source, not a fix.

## Rules

- **Never invent a finding.** Every ticket traces to the report. If the audit did not measure it, it is not a ticket.
- **Never soften a finding.** The severity in the ticket is the severity in the report.
- **Never modify or close the parent** while publishing children.
- **Never publish before the archive exists.** The spec links to it.
- **A spec is not a task.** No agent-ready label, no checkboxes, no assignee.
- **Sub-issues, not comments.** Verify the native link after creating each one.
- **Report the disclosure.** If the audit run touched anything real, it goes in the spec's Context section in bold.
