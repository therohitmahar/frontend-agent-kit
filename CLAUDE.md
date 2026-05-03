# Claude Frontend Standards

## Context

You are working in a React + Tailwind project. Apply these standards consistently in every frontend change.

## Buttons

Use a reusable `Button` component for buttons.

Support primary and secondary variants.

Include a built-in loading state with a spinner.

Disable the button whenever `loading` is true.

Never leave a button clickable during an async action.

Do not copy button class strings into multiple files when a reusable component should own them.

## Inputs

Use a reusable `Input` component for inputs.

The component must render its own `label` element.

Required fields must show a mandatory asterisk (`*`) in the label.

The component must accept a `type` prop for standard input types such as `text`, `number`, `email`, `password`, `tel`, `url`, and `date`.

Keep field rendering and field-level UI logic inside the component.

Inputs should look and behave consistently across the project.

## Forms

Validate on the client before calling an API.

Show inline field errors beside the fields that need attention.

Do not rely only on a top-level error message.

Show loading while the form is submitting.

Disable the submit button during submission.

Show a success toast after completion.

Show an error toast when submission fails.

## Toasts And Feedback

Every user action needs feedback.

Use a consistent success, error, and info toast pattern.

Never let an action complete silently.

Prefer a small reusable toast utility or component in the project instead of adding a toast library.

## File Uploads

Always show upload progress or a visible loading indicator during file uploads.

Never let uploads happen silently.

The user must know whether the file is uploading, processing, completed, or failed.

Disable conflicting actions while upload or processing is active.

## UI States

Every data-backed UI must handle loading, empty, and error states.

Do not show a blank area while data is loading.

Do not show an empty list without a clear empty state.

Do not hide errors without visible feedback.

## Libraries

Do not install a library unless the user explicitly asks for one.

Write the needed utility or component in the project.

Use native browser APIs and React first.

## Components

Build reusable components.

Do not duplicate logic across files when it belongs in a shared component or utility.

Keep components focused, practical, and readable.

## Styling

Use Tailwind utility classes only.

Do not use inline styles unless the user asks for them.

Keep visual patterns consistent across the project.
