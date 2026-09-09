---
name: prompt
description: Turn a rough request into a ready-to-paste prompt that names the exact skill and sub-command for the job. Use when the user types /skill:prompt with a task description, asks which skill or command fits, or wants a prompt written for them. Router over every installed skill.
disable-model-invocation: true
---

# Prompt

Input: one rough sentence about what the user wants to do.

Output: a copyable prompt that names the right skill, its exact sub-command, and a concrete target.

You write the prompt. You never do the work described in it.

## Process

1. Read [CATALOG.md](CATALOG.md): the inventory of installed skills, their trigger descriptions, and their sub-commands. Regenerate with `node scripts/build-catalog.mjs` when a skill in it is missing or renamed.
2. Read [ROUTING.md](ROUTING.md): the rules for picking between skills that overlap, and the symptom-to-command map for `impeccable`.
3. Make the target concrete. When the cwd is a project and the request names a page, section, route, or component, run at most one `rg` or `ls` to find the real path. One search only; if it misses, use the user's own words as the target.
4. Confirm the sub-command. Before naming a command, read the chosen skill's `SKILL.md` command table and copy the command verbatim. Never invent a command name.
5. Emit the prompt in the format below.

## Rules

- One primary prompt. Add a second, labelled alternative only when a different reading of the request routes to a different skill. Never three.
- Ask at most one question, and only when the skill choice cannot be resolved without it. Emit your best-guess prompt anyway, above the question.
- When no skill fits, say so in one line and emit a plain prompt with no skill in it. A forced skill is worse than none.
- Never edit files, run the work, or start the task. The prompt is the whole deliverable.
- Presets (`minimalist-ui`, `industrial-brutalist-ui`) and checks (`web-design-guidelines`, `code-review`) layer on top of a build skill. They go in the `Then:` line or as a constraint, never as the primary skill.
- Keep the prompt body at 10 lines or fewer. Name the file or route, state the outcome, state what must not change, state what the user will see when it works.
- No vague adjectives in the prompt ("modern", "clean", "better"). Replace with the observable change the user wants.
- The `unslop` skill applies to the prompt text you write.

## Invocation syntax

Default to pi syntax: `/skill:<name> <command> <target>`.

In Claude Code the same skill is `/<name>`. Only mention this when the user says they are pasting into another harness.

Some answers are not skills. `/ship`, `/clear`, and `/compact` are pi commands; emit them bare when they are the right answer.

## Output format

Line 1: the pick, in 20 words or fewer. Skill, command, why.

Then one fenced block, exactly this shape:

```
/skill:impeccable polish src/components/pricing-section.tsx

Tighten spacing, alignment, and state coverage on the pricing section before launch.
Keep the copy, prices, and component API as they are.
Done when the three cards share one baseline grid and hover, focus, and disabled states all read.
```

Optional last line, only when a follow-up genuinely helps:

`Then: /skill:web-design-guidelines src/components/pricing-section.tsx`

Close the reply with a `Next` block telling the user to paste the block. Skip the `What to check` block; nothing changed on disk.
