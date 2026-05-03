# Contributing

This repo is small on purpose. Every contribution should make AI-generated frontend code more consistent, more production-ready, or easier to review.

## Quality Bar

No skill file is accepted without a realistic before/after code example.

The before example must show a real bad pattern an AI agent commonly generates. The after example must show the corrected React + Tailwind pattern clearly enough to copy into a project.

We reject PRs that add libraries the user did not ask for.

One skill per PR.

Be direct. Be respectful. Have taste.

## Skill File Template

Every skill file must follow this structure:

```md
# Skill Name

## Problem
One sentence: what does AI get wrong without this skill?

## Instruction
The exact instruction to paste into CODEX.md or your agent instruction file.

## Example Prompt
A realistic prompt a developer would write using this skill.

## Before (without this skill)
Realistic code example showing what Codex generates without this instruction.
Show the bad pattern clearly with actual React + Tailwind code.

## After (with this skill)
What Codex generates when this skill is active.
Show the correct pattern with actual React + Tailwind code.

## Notes
Edge cases, stack-specific notes, things to watch out for.
```

## Writing Standards

Write skill files for working developers. Do not pad the instructions. Do not turn examples into toy snippets when the problem is practical.

Use React + Tailwind examples unless the skill explicitly targets another stack.

Prefer native browser and React APIs. Do not introduce a dependency unless the skill is specifically about integrating a dependency the user asked for.
