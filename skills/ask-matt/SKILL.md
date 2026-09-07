---
name: ask-matt
description: Ask which skill or flow fits your situation. A router over the skills in this repo.
disable-model-invocation: true
---

# Ask Matt

You don't remember every skill, so ask.

A **flow** is a path through the skills. Most paths run along one **main flow**, and two **on-ramps** merge onto it. Off that spine sit four families: **frontend and design**, **delegation**, **standalone** one-offs, and the **vocabulary** layers that run underneath everything.

## The main flow: idea → ship

The route most work travels. You have an idea and want it built.

1. **`/grill-with-docs`** sharpens the idea by interview. Start here whenever you are **working in a working directory**: it's stateful, retaining what it learns in `CONTEXT.md` and ADRs. (No working directory? Use `/grilling` directly, covered under Standalone. `grill-with-docs` wraps that same primitive and leaves a paper trail, which makes it the better of the two whenever a repo is there to leave it in.)
2. **Branch: can you settle every question in conversation?** If a question needs a runnable answer (state, business logic, a UI you have to see), detour through a prototype, bridged by **`/handoff`** in both directions (a prototype lives in its own directory, which is exactly what `/handoff` is for; see Phase boundaries):
   - **`/handoff`** out, then open a fresh session against that file,
   - **`/prototype`** to answer the question with throwaway code,
   - **`/handoff`** back what you learned, and reference it from the original idea thread.
3. **Branch: is this a multi-session build?**
   - **Yes** → **`/to-spec`** (turn the thread into a spec), then **`/to-tickets`** to split it into tracer-bullet tickets, each declaring its **blocking edges**. On a local tracker that's one file per ticket under `.scratch/<feature>/issues/`, worked blockers-first by hand; on a real tracker the edges become native blocking links, so any ticket whose blockers are done can be grabbed: kick off **`/implement`** per ticket, **`/clear`ing context between each one**. Each ticket is self-contained, so the last one's context is disposable.
   - **No** → **`/implement`** right here, in the same context window.

   Either way, **`/implement`** builds each issue by driving **`/tdd`** internally (one red-green slice at a time), then closes out by running **`/code-review`**, a two-axis review (Standards + Spec) of the diff, before committing. Reach for **`/tdd`** on its own when you just want to build a concrete behaviour test-first without a full spec, and **`/code-review`** on its own whenever you want to review a branch or PR against a fixed point.

### Context hygiene

Keep steps 1–3 in **one unbroken context window** (don't compact or clear until after `/to-tickets`) so the grilling, spec, and tickets all build on the same thinking. Each `/implement` then starts fresh, working from the ticket.

The limit on this is the **[smart zone](https://www.aihero.dev/ai-coding-dictionary/smart-zone)**: the window (~150k tokens on state-of-the-art models) within which the model still reasons sharply. If a session approaches it before `/to-tickets`, don't push on degraded; `/compact` at the nearest phase boundary and carry on (see Phase boundaries).

## On-ramps

A starting situation that generates work, then merges onto the main flow.

- **Bugs and requests piling up** → **`/triage`**. It moves issues through triage roles and produces agent-ready issues, which **`/implement`** later picks up.

  Triage is only for issues **you didn't create**: bug reports, incoming feature requests, anything that arrives raw. Tickets that `/to-tickets` produced are already agent-ready, so **don't triage them**.

- **Something's broken** → **`/diagnosing-bugs`**. For the hard ones: the bug that resists a first glance, the intermittent flake, the regression that crept in between two known-good states. It refuses to theorise until it has a **tight feedback loop** (one command that already goes red on *this* bug), then fixes with a regression test. Its post-mortem hands off to **`/improve-codebase-architecture`** when the real finding is that there's no good seam to lock the bug down.

- **A huge, foggy effort: a greenfield project or a huge feature build, too big for one session** → **`/wayfinder`**, the most cognitively demanding flow here. When the way from here to the destination isn't visible yet, it charts a **shared map** of **decision tickets** on the issue tracker and resolves them one at a time, producing **decisions, not deliverables**, until the fog is pushed back and the way is clear. Where **`/grill-with-docs`** sharpens an idea you can hold in one session, wayfinder is for the idea you can't, and it's slower and denser, so save it for exactly that, never a well-scoped feature.

  When the map clears, **it hands off, it doesn't build**: merge onto the main flow at **`/to-spec`**, which collapses the map's linked decisions into a buildable plan, then `/to-tickets` and `/implement` as usual. Looping the map straight into `/implement` skips that collapse and throws the linked detail away, so go straight to `/implement` only when the effort turned out genuinely small.

## Codebase health

Not feature work, just upkeep.

- **`/improve-codebase-architecture`** runs whenever you have a spare moment to keep the codebase good for agents to operate in. It surfaces **deepening opportunities**; picking one _generates an idea_ you can take into the main flow at `/grill-with-docs`. It's the survey that finds the candidates; **`/codebase-design`** (below) is the bench you design the chosen one on.
- **`/setup-ts-deep-modules`** is the one-shot enforcement pass for a TypeScript repo: it wires up dependency-cruiser so each package's entry-point files are the only way in and its subfolders stay hidden. Run it once per repo, so the rules bite on every later change rather than being re-argued in review.

## Frontend and design

The biggest cluster, and the easiest to pick wrong. Choose by **what you are producing**, not by which name sounds best. Exactly one build skill drives a task; presets and checks layer on top of it.

### Producing images, no code

- **`/imagegen-frontend-web`**: one horizontal reference image **per section** of a site. 8 sections means 8 images.
- **`/imagegen-frontend-mobile`**: app screens and flows, framed in phone mockups.
- **`/brandkit`**: logo systems, identity decks, brand-guideline boards.
- **`/image-to-code`** is the bridge between this group and the next: generate the references first, then build to match them. Reach for it when the look matters more than the spec and you want to see it before it's coded.

### Producing code

Pick one. They are alternatives, not a stack.

- **Existing UI, something specific is wrong** (review, audit, critique, polish, harden, a11y, motion, copy) → **`/impeccable`**. The default for anything already on screen, and model-invoked, so it usually fires without being asked. It ends in a list of recommended commands, which is its **on-ramp to the main flow**: **`/impeccable-to-tickets`** turns those into a spec plus one sub-issue per fix, then **`/impeccable-implement`** works a single ticket from its URL under `/implement`'s discipline.
- **Existing UI, "just make it look better"** → **`/redesign-existing-projects`**. Audits the current design, strips generic AI patterns, applies premium standards without breaking behaviour. `/impeccable` when you can name the defect; this when you can't.
- **New landing page, portfolio, or marketing site** → **`/design-taste-frontend`**. The greenfield default. (`/design-taste-frontend-v1` exists only for projects pinned to the old behaviour. Don't reach for it.)
- **New build where scroll motion is the point** → **`/gpt-taste`**. GSAP pinning, stacking, scrubbing, AIDA structure. Only when the motion is a requirement.
- **New build where "make it feel expensive" is the brief** → **`/high-end-visual-design`**. Agency-tier depth, shadow, spacing, micro-interaction.
- **Generating screens in Google Stitch** → **`/stitch-design-taste`**. It writes the `DESIGN.md` Stitch reads. Not for hand-written code.

### Presets, layered on top

These fix palette, type, and surface treatment. They decide how it looks, not how the work runs, so they go **with** a build skill, never instead of one.

- **`/minimalist-ui`**: warm monochrome, editorial, flat bento, no gradients or heavy shadow.
- **`/industrial-brutalist-ui`**: Swiss print meets military terminal. Rigid grids, extreme type contrast, analog degradation.

### The check afterwards

- **`/web-design-guidelines`** reviews UI code against the Web Interface Guidelines: accessibility, focus, states, semantics. Run it after any build skill above, the way `/code-review` closes out `/implement`.

## Delegation

Move work out of this context and onto another agent, through [Paseo](https://paseo.sh). `/handoff` (under Standalone) is the portable-file alternative when the receiving agent isn't a Paseo one.

- **`/paseo-advisor`**: one agent, one second opinion. You keep the work.
- **`/paseo-committee`**: two high-reasoning agents do root-cause analysis in parallel and return a plan. For when you're stuck, looping, or tunnel-visioning.
- **`/paseo-handoff`**: hand the whole task over with a self-contained briefing, since the receiving agent starts at zero context.
- **`/paseo`**: the reference for driving projects, workspaces, scripts, agents, schedules and heartbeats over MCP or the CLI.
- **`/paseo-plugin`**: build, install, or debug a local Paseo plugin.
- **`/paseo-help`**: Paseo itself is misconfigured or broken.

## Vocabulary underneath

Three model-invoked references that run *beneath* the other skills, each the single source of truth for its vocabulary. Reach for them directly when the **words**, not the process, are the problem; or let the skills above pull them in.

- **`/domain-modeling`**: sharpen the project's *domain* language: challenge a fuzzy term, resolve an overloaded word ("account" doing three jobs), record a hard-to-reverse decision as an ADR. It's the active discipline `/grill-with-docs` drives to keep `CONTEXT.md` a clean glossary.
- **`/codebase-design`** is the deep-module vocabulary (module, interface, depth, seam, adapter, leverage, locality) for designing a module's *shape*: a lot of behaviour behind a small interface at a clean seam. `/tdd` and `/improve-codebase-architecture` both speak it.
- **`/frontend-design`** is the same idea for how a thing *looks*: palette, typography, and aesthetic direction that reads as deliberate rather than templated. It gives the words for a visual point of view; the build skills under Frontend and design do the work.

## Phase boundaries

A **phase** is a chunk of work inside a session: the grilling, the implementation, the QA. At the **boundary** between two of them you have five options, and picking between them is the fuzziest decision in this whole map:

- **Continue**: stay put. Costs nothing, loses nothing.
- **`/clear`**: empty the window, when nothing here matters to what's next.
- **`/handoff`** writes a portable markdown file. Narrow: only for a **new harness**, a **new directory**, a **colleague**, or forking a side task **mid-phase**. What it buys is portability.
- **Subagent**: send a tightly-scoped task to its own window and get a report back.
- **`/compact`** compresses this context and seeds a fresh session with it. The **default**, at the bottom of the tree rather than the first reach.

Read [PHASE-BOUNDARIES.md](PHASE-BOUNDARIES.md) for the ordered tree: the five questions, the reasoning behind each branch, and why the primary-source cost makes **Continue** the one to rule out first. Make the decision **at** a boundary; mid-phase, continue or split the rest into subagents.

## Standalone

Off the main flow entirely.

- **`/grilling`** is the interview primitive: rounds, the frontier, facts are the agent's job and decisions are yours. It is also the **stateless** way in, so reach for it directly when you are **not working in a working directory** (sharpening a plan, a design, a piece of writing, anything with no repo under it). In a repo, prefer `/grill-with-docs`: same interview, plus a paper trail. `/triage`, `/wayfinder` and `/improve-codebase-architecture` all run this primitive internally.
- **`/resolving-merge-conflicts`** works an in-progress merge or rebase conflict hunk by hunk, resolving by **intent** traced to each side's primary source rather than by picking lines, then finishes the operation. It never runs `--abort`. Standalone and off every flow: reach for it when you are already mid-conflict.
- **`/prototype`** is a small, throwaway program that answers one design question: does this state model feel right, or what should this UI look like. Throwaway is a constraint on how the code is written, not a promise to destroy it: the answer folds into the real code, and the prototype itself is kept as a **primary source** on a `prototype/<name>` branch out of main, pointed at from the implementation issue. It's the detour in step 2 of the main flow, but reach for it any time a design question is hard to settle on paper.
- **`/research`**: delegate reading legwork to a **background agent**: it investigates a question against **primary sources**, then leaves a cited Markdown file in the repo. Keep working while it reads. The file it produces is something to take *into* the main flow at `/grill-with-docs`, since research feeds the thinking rather than replacing it.
- **`/to-questionnaire`** comes in when the thing blocking you isn't in your head or the codebase but in **someone else's**, and it writes them a questionnaire to fill in. It's the inverse of `/grilling`: instead of interviewing you about the subject, it interviews you about the **send** (who it's going to, what you need back) and aims the questions at the gap. What comes back is material for `/grill-with-docs` or `/to-spec`.
- **`/wizard`** is for the steps only a **human** can take: provisioning infrastructure, setting up credentials or CI secrets, clicking through an unfamiliar third-party dashboard, running a one-off migration or cutover. It generates an interactive bash script that opens each URL, captures each value, and writes it into `.env` and GitHub secrets, so the procedure stops being something you re-explain to an agent every time. Model-invoked, so the agent reaches for it the moment it hits a wall only you can pass. If the agent could just do it itself, it should; this is for where a human is genuinely in the loop.
- **`/wait-what`** is the corrective for a message that didn't land. Use it mid-conversation, inside any other skill, and the agent re-pitches what it just said with the context you were missing, in plain English, using the `CONTEXT.md` vocabulary. It works after the fact; `/grill-with-docs` is the upfront cure, because a shared language agreed early is what stops the jargon arriving at all.
- **`/teach`**: learn a concept over multiple sessions, using the current directory as a stateful workspace.
- **`/writing-for-agents`** is the reference for writing documents agents consume: skills, AGENTS.md, pointed-at docs.
- **`/full-output-enforcement`** bans truncation and placeholder stubs (`// ... rest of the code stays the same`). Load it when a task must emit whole files rather than a diff-shaped sketch.

## Always on

No trigger, no flow position. These apply to every task.

- **`/unslop`** cuts AI tells from any writing: puffery, filler, em dashes, "not just X but Y". It applies to docs, comments, commit messages and replies, always. `/writing-for-agents` is its counterpart for documents an *agent* reads, and `/frontend-design` plays the same role for visual language.

## Precondition

The engineering flow reads two per-repo config docs that the skills do **not** create for you:

- **`docs/agents/issue-tracker.md`**: where issues live (GitHub via `gh`, GitLab via `glab`, or local markdown under `.scratch/<feature>/issues/`). Read by `/to-spec`, `/to-tickets`, `/triage`, `/wayfinder`, `/code-review`.
- **`docs/agents/triage-labels.md`**: the label strings for the five canonical triage roles (`needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`). Read by `/triage`.

When either is missing, the skills ask you once and fall back to local markdown plus the canonical label names. Write the docs when you want to stop being asked.
