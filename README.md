# frontend-agent-kit

Copy-paste frontend standards for AI coding agents working in React + Tailwind projects.

Use this kit when you want agents to create reusable components, show visible feedback, validate forms before API calls, and handle loading, empty, and error states without adding unnecessary dependencies.

## Why this exists

AI agents will often produce working UI that is still incomplete: one-off buttons, duplicated input classes, silent writes, vague form errors, silent uploads, and blank data views.

This repo gives you reusable instruction files and focused skill files so those standards travel with the project instead of being re-explained in every session.

## What is included

- `CODEX.md`: primary instruction file for OpenAI Codex
- `CLAUDE.md`: equivalent instruction file for Claude
- `AGENTS.md`: generic agent instruction file
- `skills/`: focused copy-paste standards for one frontend concern at a time
- `templates/react-tailwind/CODEX.md`: combined React + Tailwind instruction template with baseline component snippets

## How to use

Pick the smallest file that fits your project.

1. Copy `CODEX.md` into your project root if you use OpenAI Codex.
2. Copy `CLAUDE.md` or `AGENTS.md` into your project root if you use another agent.
3. Copy individual files from `skills/` when you only want one standard.
4. Copy `templates/react-tailwind/CODEX.md` when you want the complete React + Tailwind version with baseline code examples.

No install. No CLI. No dependencies.

## Skills

| File | Use when you want the agent to... |
| --- | --- |
| `skills/button-component.md` | Create a reusable `Button` with primary and secondary variants, loading state, spinner, and disabled async behavior. |
| `skills/input-component.md` | Create a reusable labeled `Input` with required markers, native `type`, inline errors, and consistent styling. |
| `skills/forms-and-validation.md` | Validate before API calls, show inline errors, prevent duplicate submits, and toast success or failure. |
| `skills/toast-and-feedback.md` | Make saves, deletes, uploads, copies, sends, and status changes visibly acknowledge success, error, or progress. |
| `skills/loading-empty-error-states.md` | Render complete loading, empty, and error states for data-backed UIs. |
| `skills/file-upload-ux.md` | Show upload progress or visible loading, disable conflicting actions, and report completion or failure. |

## Philosophy

- Native first: use browser APIs, React, and small local utilities before dependencies.
- Reusable components: repeated UI and behavior belongs in a component or utility.
- Complete states: loading, empty, error, disabled, success, and failure states are part of the feature.
- Visible feedback: user actions should never complete silently.
- Practical scope: keep the project small unless the user explicitly asks for more.

## Recommended Review Checklist

- Buttons use a reusable `Button` component.
- Async actions show loading and disable conflicting controls.
- Inputs use a reusable `Input` component with labels and required markers.
- Forms validate on the client before API calls.
- Field errors are inline and specific.
- User actions show success, error, or info feedback.
- File uploads show progress or clear loading and processing states.
- Data-backed UIs handle loading, empty, and error states.
- No dependency was added unless explicitly requested.
- Styling uses Tailwind utilities only.

## Contributing

See `CONTRIBUTING.md`.
