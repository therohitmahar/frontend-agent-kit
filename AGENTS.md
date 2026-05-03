# Generic AI Agent Frontend Standards

## Project Context

This is a React + Tailwind project. Follow these standards for every frontend task.

## Button Standards

Always use a reusable `Button` component.

The component must provide primary and secondary variants.

The component must provide a built-in loading state with a spinner.

The component must disable itself while loading.

Never leave a button clickable during an async action.

Avoid repeated button class names and repeated button logic across files.

## Input Standards

Always use a reusable `Input` component.

The component must render its own `label`.

Required fields must show a mandatory asterisk (`*`) in the label.

The component must accept a `type` prop for standard input behavior.

All input rendering and field-level logic should live inside the component.

Input behavior and styling must stay consistent across the project.

## Form Standards

Validate form data on the client before making an API request.

Show inline field errors for specific fields.

Do not use only a top-level error message.

Show loading while submitting.

Disable the submit button while submitting.

Show a success toast after completion.

Show an error toast when submission fails.

## Toast And Feedback Standards

Every user action must produce visible feedback.

Use one consistent pattern for success, error, and info toasts.

Never leave user actions silent.

Prefer writing a small reusable toast utility or component instead of installing a toast package.

## File Upload Standards

Always show upload progress or a visible loading state during file upload.

Never let a file upload happen silently.

The user must know when the file is uploading, processing, completed, or failed.

Disable conflicting actions while upload or processing is active.

## State Standards

Every data-backed UI must handle loading, empty, and error states.

Never render a blank section while loading.

Never render an empty list without an empty state.

Never swallow errors without visible feedback.

## Library Standard

Never install a library unless explicitly asked.

Write the required utility or component in the project.

Use native browser APIs and React before reaching for dependencies.

## Component Standard

Prefer reusable components over copied code.

Never duplicate logic across files when it belongs in a shared component or utility.

Keep abstractions practical and easy to understand.

## Styling Standard

Use Tailwind utility classes only.

Do not use inline styles unless explicitly asked.

Keep spacing, states, and component variants consistent.
