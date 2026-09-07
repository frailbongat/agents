# agent skills

My personal agent skill library, synced to `~/.agents`.

Most of these skills are not mine. They are installed from other public repos
and kept here so my agents can load them. `.skill-lock.json` records where each
one came from and which upstream commit it matches, for the skills the installer
manages. `impeccable` and the `paseo-*` skills were installed by their own tools
instead, so their provenance lives in [NOTICE.md](NOTICE.md) only.

`~/.pi/agent/skills/` holds a symlink per skill pointing back into this repo, so
there is exactly one copy of each on disk. Start at `skills/ask-matt/SKILL.md`
for a router over everything here.

## Licensing

Read [NOTICE.md](NOTICE.md) before reusing anything. Short version:

| Origin | Skills | License |
| --- | --- | --- |
| [mattpocock/skills](https://github.com/mattpocock/skills) | 24 | MIT |
| [Leonxlnx/taste-skill](https://github.com/Leonxlnx/taste-skill) | 13 | MIT |
| [getpaseo/paseo](https://github.com/getpaseo/paseo) | 6 `paseo-*` | Apache 2.0 |
| [pbakaus/impeccable](https://github.com/pbakaus/impeccable) | `impeccable` | Apache 2.0 |
| [anthropics/skills](https://github.com/anthropics/skills) | `frontend-design` | Apache 2.0 |
| [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills) | `web-design-guidelines` | None declared upstream |
| Mine | `unslop`, `impeccable-to-tickets`, `impeccable-implement` | MIT |

The root [LICENSE](LICENSE) covers only my own work. Everything else keeps its
original license.

If you own any of this and want it removed or attributed differently, open an
issue and I will fix it.
