# Codex Frontend Standards

## Project Context

This is a React + Tailwind project. Apply these standards to every frontend change unless the user explicitly overrides them.

## Core Philosophy

Use native browser APIs, React, and small local utilities before dependencies.

Reusable components beat copied code.

Loading, empty, error, disabled, success, and failure states are part of the feature.

User actions must never complete silently.

## Button Rules

Always create and use a reusable `Button` component for project buttons.

The `Button` component must support primary and secondary variants.

The `Button` component must include a built-in loading state with a spinner.

When `loading` is true, the button must be disabled.

The `Button` component should default to `type="button"` to avoid accidental form submits.

Never leave a button clickable during an async action.

Do not duplicate button classes across files when the reusable component should be used.

## Input Rules

Always create and use a reusable `Input` component for project inputs.

The `Input` component must include its own `label` tag.

When a field is required, show a mandatory asterisk (`*`) in the label.

The `Input` component must accept a `type` prop for values like `text`, `number`, `email`, `password`, `tel`, `url`, `date`, and other valid input types.

The `Input` component must support inline field errors.

All input rendering and field-level UI logic must live inside the component.

Inputs must be consistent across the entire project.

## Form Rules

Validate form data on the client before submitting to an API.

Show inline field errors next to the relevant fields.

Do not rely only on a top-level error message.

Always show a loading state while submitting.

Disable the submit button while submitting.

Prevent duplicate submissions.

Show success feedback with a toast after completion.

Show error feedback with a toast when submission fails.

## Toast Rules

Every user action must receive feedback.

Use a consistent success, error, and info toast pattern.

Never leave a user action silent.

Use success to confirm completion, error when an action fails, and info for neutral or in-progress feedback.

Prefer a small reusable toast utility or component written in the project over installing a toast library.

## File Upload Rules

Always show upload progress or a visible loading indicator during file upload.

Never let a file upload happen silently.

The user must always know the file is being uploaded, processed, completed, or failed.

Disable conflicting actions while upload or processing is active.

Show info feedback when upload starts, success feedback when upload completes, and error feedback when upload fails.

## State Rules

Every UI that depends on data must handle loading state, empty state, and error state.

Do not render a blank area while data is loading.

Do not render an empty list without a clear empty state.

Do not swallow errors without visible feedback.

Errors should usually include a retry action.

## Library Rule

Never install a library unless explicitly asked.

Write the utility or component in the project instead of adding a dependency.

Use native browser APIs and React first.

## Component Rule

Build reusable components.

Never duplicate logic across files when it belongs in a shared component or utility.

Keep components small, practical, and easy to read.

Do not invent abstractions before there is real repetition or shared behavior.

## Style Rule

Use Tailwind utility classes only.

Do not use inline styles unless explicitly asked.

Keep visual patterns consistent across the project.

Use accessible focus states on interactive elements.

## Review Checklist

Before finishing any frontend task, check that buttons, inputs, forms, uploads, user feedback, and data-backed views follow these standards, and that no dependency was added unless explicitly requested.
