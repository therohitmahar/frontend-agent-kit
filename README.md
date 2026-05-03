# Stop re-explaining your frontend standards to every AI session.

`frontend-agent-kit` is a copy-paste instruction kit for developers who want AI coding agents to build React + Tailwind interfaces with consistent components, visible feedback, and complete UI states. It is for developers who care about their code and do not want to add a library for everything. It is not for vibe coders.

## Why this exists

AI agents forget your frontend standards unless you restate them every session. This repo was born from repeatedly telling Codex the same frontend standards on every new project: reusable buttons, proper inputs, visible feedback, real form validation, upload progress, and complete loading, empty, and error states.

## How to use

Option 1: Copy `CODEX.md` into your project root. This is the primary path for OpenAI Codex.

Option 2: Copy `CLAUDE.md` or `AGENTS.md` into your project root if you use a different agent.

Option 3: Copy individual skill files from `/skills` when you only want one standard.

Option 4: Use the full combined template from `/templates/react-tailwind/`.

No install. No CLI. No dependencies. Copy a file. Ship better frontend.

## Skills

| Skill name | Description | Problem it solves |
| --- | --- | --- |
| Button component | Defines reusable primary and secondary buttons with loading and disabled states. | Prevents async actions from staying clickable and eliminates one-off button code. |
| Input component | Defines labeled, required-aware, typed inputs with internal logic. | Prevents inconsistent labels, missing required markers, and duplicated input handling. |
| Forms and validation | Defines client validation, inline errors, submit loading, and success feedback. | Prevents silent or invalid API calls and weak form UX. |
| Toast and feedback | Defines consistent success, error, and info feedback after user actions. | Prevents user actions from completing silently. |
| Loading, empty, error states | Defines required UI states for every data-backed interface. | Prevents blank screens and incomplete production flows. |
| File upload UX | Defines visible progress or loading during upload and processing. | Prevents silent uploads and unclear file processing. |

## Philosophy

1. Native first — no library unless you ask for one
2. Production quality — loading, empty, error states are not optional
3. Composable — use one skill or all of them

## Contributing

See `CONTRIBUTING.md`.

Not for everyone. Built for developers who know what good frontend looks like.
