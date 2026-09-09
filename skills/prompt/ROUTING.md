# Routing

Judgment rules for picking one skill. [CATALOG.md](CATALOG.md) is the inventory; this file is the tiebreaker. When they disagree about a command name, the skill's own `SKILL.md` wins.

## First cut

Ask what the user is producing.

| They want | Go to |
|---|---|
| An existing UI changed or reviewed | [Frontend, existing UI](#frontend-existing-ui) |
| A new UI built | [Frontend, new build](#frontend-new-build) |
| Reference images, no code | `imagegen-frontend-web` (one image per section), `imagegen-frontend-mobile` (phone screens), `brandkit` (logo and identity boards) |
| Images first, then code to match | `image-to-code` |
| A feature built from an idea | [The build flow](#the-build-flow) |
| Something broken diagnosed | `diagnosing-bugs` |
| Reading or facts gathered | `research` |
| Work moved to another agent | [Delegation](#delegation) |
| Writing cleaned up or a doc written | `unslop`, or `writing-for-agents` for skills and AGENTS.md |
| A browser driven | `playwright-cli` |
| Their changes committed and pushed | `/ship` (a pi command, not a skill) |

## Frontend, existing UI

`impeccable` is the default whenever the user can name the defect. It carries 22 commands; picking the wrong one wastes the run.

Match the user's own words to a command:

| They said | Command |
|---|---|
| "review it", "is this good", "give me a critique", "score the UX" | `critique` |
| "check a11y", "check performance and responsive", "audit it" | `audit` |
| "tidy it up", "final pass", "almost shipping" | `polish` |
| "boring", "safe", "bland", "make it pop" | `bolder` |
| "too much", "too loud", "calm it down" | `quieter` |
| "too busy", "strip it back", "simplify" | `distill` |
| "production ready", "error states", "edge cases", "i18n" | `harden` |
| "first-run", "empty state", "onboarding", "activation" | `onboard` |
| "add motion", "animate it", "feels static" | `animate` |
| "add color", "too grey", "monochrome" | `colorize` |
| "fonts", "type hierarchy", "headings look off" | `typeset` |
| "spacing", "alignment", "cramped", "rhythm", "hierarchy" | `layout` |
| "add personality", "memorable", "fun detail" | `delight` |
| "go extreme", "push it way past normal" | `overdrive` |
| "copy", "labels", "error messages", "wording" | `clarify` |
| "mobile", "tablet", "small screens", "responsive breakage" | `adapt` |
| "slow", "janky", "laggy scroll", "reflow" | `optimize` |
| "plan the UX first", "before we code" | `shape` |
| "pull out tokens", "make it reusable", "design system" | `extract` |
| "write down our product context" | `init` |
| "document the design that exists" | `document` |
| "let me pick elements in the browser and try variants" | `live` |

New surface or full visual replacement: use `impeccable` with no command; it loads its new-work playbook.

Two neighbours to `impeccable`:

- `redesign-existing-projects` when the brief is "just make it look better" and the user cannot name the defect.
- `web-design-guidelines` as the check after any build, not as the build itself.

The `impeccable` output ends in recommended fixes. `impeccable-to-tickets` turns those into a spec plus one ticket each; `impeccable-implement` works a single ticket.

## Frontend, new build

Pick exactly one. These are alternatives, not a stack.

- `design-taste-frontend`: the default for a new landing page, portfolio, or marketing site.
- `gpt-taste`: only when scroll motion is the requirement (GSAP pinning, stacking, scrubbing).
- `high-end-visual-design`: when the brief is "make it feel expensive".
- `stitch-design-taste`: only when generating screens in Google Stitch.
- `img2threejs`: an image turned into a Three.js model.

Layer a preset on top when the user names a look: `minimalist-ui` (warm monochrome, editorial, flat) or `industrial-brutalist-ui` (Swiss grid, terminal, degradation). `frontend-design` is vocabulary, not a build skill.

## The build flow

Idea to shipped feature, in order. Do not skip ahead when the earlier step is missing.

1. `grill-with-docs` sharpens a fuzzy idea inside a repo. `grilling` is the same interview with no repo under it.
2. `prototype` answers a design question that only runnable code can settle.
3. `to-spec` turns the thread into a spec, `to-tickets` splits it into tickets.
4. `implement` builds one ticket; it drives `tdd` inside and closes with `code-review`.
5. `tdd` alone for one concrete behaviour, `code-review` alone to review a diff since a fixed point.

On-ramps: `triage` for incoming bugs and requests the user did not write, `wayfinder` for a foggy greenfield effort too big for one session.

Upkeep: `improve-codebase-architecture` to find deepening opportunities, `codebase-design` for the module vocabulary, `setup-ts-deep-modules` once per TypeScript repo, `resolving-merge-conflicts` mid-conflict, `domain-modeling` for CONTEXT.md and ADRs.

## Delegation

- `paseo-advisor`: one second opinion, the user keeps the work.
- `paseo-committee`: two agents do root-cause analysis and return a plan. For being stuck or looping.
- `paseo-handoff`: hand the whole task to another Paseo agent.
- `handoff`: portable markdown file for a different harness, directory, or person.
- `paseo`, `paseo-plugin`, `paseo-help`: driving Paseo, building a plugin, fixing Paseo itself.
- Superset equivalents live under `superset/skills/`: `orchestrate`, `automate`, `standup`, `browser`, `computer`, `page`, `setup`, `doctor`, `10x`.

## Standalone

`wizard` for steps only a human can take (credentials, dashboards, cutovers). `to-questionnaire` when the missing answer is in someone else's head. `wait-what` when an explanation did not land. `teach` for learning a concept over sessions. `full-output-enforcement` when whole files must be emitted with no stubs. `grilling` to stress-test a plan.

## When nothing fits

Say it in one line and write a plain prompt. Backend work, config changes, one-off scripts, and questions about a codebase have no skill and do not need one.
