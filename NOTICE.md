# Third-party notices

Most skills in this repository were installed from other people's public repos.
They keep their original licenses and copyright. Only the items listed under
"Original work" are covered by the root `LICENSE`.

## Original work

| Path | Author | License |
| --- | --- | --- |
| `skills/unslop/` | Frail Bongat | MIT (root `LICENSE`) |
| `skills/impeccable-to-tickets/` | Frail Bongat | MIT (root `LICENSE`) |
| `skills/impeccable-implement/` | Frail Bongat | MIT (root `LICENSE`) |
| `.gitignore`, `.skill-lock.json`, `README.md`, `NOTICE.md` | Frail Bongat | MIT (root `LICENSE`) |

`impeccable-to-tickets` and `impeccable-implement` are original work that drives
the third-party `impeccable` skill listed below. They contain no upstream code.

## Redistributed work

### mattpocock/skills

- Source: https://github.com/mattpocock/skills
- License: MIT, Copyright (c) 2026 Matt Pocock
- Full text: [`licenses/mattpocock-skills-MIT.txt`](licenses/mattpocock-skills-MIT.txt)

Covers these directories under `skills/`:

`ask-matt`, `code-review`, `codebase-design`, `diagnosing-bugs`,
`domain-modeling`, `grill-with-docs`, `grilling`, `handoff`, `implement`,
`improve-codebase-architecture`, `prototype`, `research`,
`resolving-merge-conflicts`, `setup-ts-deep-modules`, `tdd`, `teach`,
`to-questionnaire`, `to-spec`, `to-tickets`, `triage`, `wait-what`,
`wayfinder`, `wizard`, `writing-for-agents`

Modified from upstream: `skills/implement/SKILL.md` is a local rewrite. It keeps
the upstream name but replaces the body with a custom acceptance-map and
freeze-gate workflow, and it is held back from upstream updates.

### anthropics/skills

- Source: https://github.com/anthropics/skills
- License: Apache License 2.0
- Full text: [`skills/frontend-design/LICENSE.txt`](skills/frontend-design/LICENSE.txt)

Covers `skills/frontend-design/`. Unmodified.

### vercel-labs/agent-skills

- Source: https://github.com/vercel-labs/agent-skills
- License: **none declared upstream**

Covers `skills/web-design-guidelines/`. Unmodified.

### Leonxlnx/taste-skill

- Source: https://github.com/Leonxlnx/taste-skill
- License: MIT, Copyright (c) 2026 Leonxlnx
- Full text: [`licenses/taste-skill-MIT.txt`](licenses/taste-skill-MIT.txt)

Covers these directories under `skills/`:

`brandkit`, `design-taste-frontend`, `design-taste-frontend-v1`,
`full-output-enforcement`, `gpt-taste`, `high-end-visual-design`,
`image-to-code`, `imagegen-frontend-mobile`, `imagegen-frontend-web`,
`industrial-brutalist-ui`, `minimalist-ui`, `redesign-existing-projects`,
`stitch-design-taste`

Unmodified.

### pbakaus/impeccable

- Source: https://github.com/pbakaus/impeccable
- License: Apache License 2.0, Copyright 2025 Paul Bakaus
- Full text: [`licenses/impeccable-Apache-2.0.txt`](licenses/impeccable-Apache-2.0.txt)

Covers `skills/impeccable/` (v4.1.1). Unmodified. Installed by hand rather than
by the skill installer, so it has no `.skill-lock.json` entry; update it with
`npx impeccable` and bump the version noted here.

### getpaseo/paseo

- Source: https://github.com/getpaseo/paseo
- License: Apache License 2.0, Copyright (c) 2025-present Mohamed Boudra
- Full text: [`licenses/paseo-Apache-2.0.txt`](licenses/paseo-Apache-2.0.txt)

Covers `skills/paseo/`, `skills/paseo-advisor/`, `skills/paseo-committee/`,
`skills/paseo-handoff/`, `skills/paseo-help/`, `skills/paseo-plugin/`.

Installed by the Paseo desktop app, not by the skill installer, so these have no
`.skill-lock.json` entry. Modified from upstream: `skills/paseo-advisor/SKILL.md`
has two example skill references repointed at skills that exist here.

At the time of writing, this repository publishes no license file at its root or
in the skill folder, so no explicit redistribution grant exists. The copy here is
kept in good faith because the project distributes these skills publicly for
reuse. Copyright remains with Vercel. If you represent the project and want this
removed, open an issue and it will be deleted.
