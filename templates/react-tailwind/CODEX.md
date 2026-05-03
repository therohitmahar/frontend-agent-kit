# React + Tailwind Frontend Constitution For Codex

## Project Context

This is a React + Tailwind project. Build production-quality frontend code with reusable components, native browser APIs, complete UI states, and consistent user feedback.

These standards apply to every frontend change unless the user explicitly overrides them.

## Core Philosophy

Native first. Do not install a library unless the user explicitly asks for one.

Production quality is the baseline. Loading, empty, and error states are not optional.

Reusable components beat copied code. If the same UI logic appears twice, extract it into a component or utility.

User actions must never be silent. Saves, submits, deletes, uploads, copies, sends, and status changes all need visible feedback.

## Library Rules

Never install a package unless the user explicitly asks for it.

Use React, browser APIs, and small local utilities before reaching for dependencies.

Do not add a form library for ordinary forms.

Do not add a toast library for ordinary toasts.

Do not add an upload library for ordinary file uploads.

If a requested feature genuinely needs a library, explain why before adding it.

## Styling Rules

Use Tailwind utility classes only.

Do not use inline styles unless the user explicitly asks for them.

Keep spacing, colors, borders, focus states, disabled states, and typography consistent across components.

Use accessible focus states on interactive elements.

Do not scatter long repeated class strings across files when a reusable component should own them.

## Button Standard

Always create and use a reusable `Button` component for project buttons.

The `Button` component must support primary and secondary variants.

The `Button` component must include a built-in loading state with a spinner.

The `Button` component must be disabled when `loading` is true.

The `Button` component must default to `type="button"` to avoid accidental form submits.

Never leave a button clickable during an async action.

Use this baseline unless the project already has a better local version:

```jsx
function Button({
  children,
  variant = "primary",
  loading = false,
  disabled = false,
  type = "button",
  className = "",
  ...props
}) {
  const baseClasses =
    "inline-flex items-center justify-center gap-2 rounded-md px-4 py-2 text-sm font-medium transition focus:outline-none focus:ring-2 focus:ring-offset-2 disabled:cursor-not-allowed disabled:opacity-60";

  const variantClasses = {
    primary: "bg-blue-600 text-white hover:bg-blue-700 focus:ring-blue-500",
    secondary:
      "border border-gray-300 bg-white text-gray-900 hover:bg-gray-50 focus:ring-gray-400",
  };

  const isDisabled = disabled || loading;

  return (
    <button
      type={type}
      disabled={isDisabled}
      aria-busy={loading}
      className={`${baseClasses} ${variantClasses[variant]} ${className}`}
      {...props}
    >
      {loading ? (
        <span
          className="h-4 w-4 animate-spin rounded-full border-2 border-current border-t-transparent"
          aria-hidden="true"
        />
      ) : null}
      <span>{children}</span>
    </button>
  );
}
```

## Input Standard

Always create and use a reusable `Input` component for project inputs.

The `Input` component must include its own `label` tag.

Required fields must show a mandatory asterisk (`*`) in the label.

The `Input` component must accept a `type` prop for values like `text`, `number`, `email`, `password`, `tel`, `url`, `date`, and other valid input types.

The `Input` component must support inline field errors.

Input rendering and field-level UI logic belong inside the component.

Inputs must be consistent across the entire project.

Use this baseline unless the project already has a better local version:

```jsx
import { useId } from "react";

function Input({
  label,
  required = false,
  type = "text",
  error,
  className = "",
  id,
  ...props
}) {
  const generatedId = useId();
  const inputId = id || generatedId;
  const errorId = error ? `${inputId}-error` : undefined;

  return (
    <div className="space-y-1">
      <label htmlFor={inputId} className="block text-sm font-medium text-gray-800">
        {label}
        {required ? <span className="ml-1 text-red-600">*</span> : null}
      </label>

      <input
        id={inputId}
        type={type}
        required={required}
        aria-invalid={error ? "true" : "false"}
        aria-describedby={errorId}
        className={`w-full rounded-md border px-3 py-2 text-sm text-gray-900 outline-none transition placeholder:text-gray-400 focus:ring-2 ${
          error
            ? "border-red-500 focus:border-red-500 focus:ring-red-100"
            : "border-gray-300 focus:border-blue-500 focus:ring-blue-100"
        } ${className}`}
        {...props}
      />

      {error ? (
        <p id={errorId} className="text-sm text-red-600">
          {error}
        </p>
      ) : null}
    </div>
  );
}
```

## Toast And Feedback Standard

Every user action must receive feedback.

Use a consistent success, error, and info toast pattern.

Never leave a user action silent.

Prefer a small reusable toast utility or component written in the project over installing a toast library.

Use success to confirm completion.

Use error when an action fails.

Use info for neutral or in-progress feedback.

Use this baseline unless the project already has a better local version:

```jsx
function Toast({ toast, onClose }) {
  if (!toast) {
    return null;
  }

  const classes = {
    success: "border-green-200 bg-green-50 text-green-800",
    error: "border-red-200 bg-red-50 text-red-800",
    info: "border-blue-200 bg-blue-50 text-blue-800",
  };

  return (
    <div
      role="status"
      className={`rounded-md border px-4 py-3 text-sm shadow-sm ${classes[toast.type]}`}
    >
      <div className="flex items-start justify-between gap-4">
        <p>{toast.message}</p>
        <button type="button" onClick={onClose} className="font-medium">
          Dismiss
        </button>
      </div>
    </div>
  );
}
```

## Form Standard

Validate form data on the client before submitting to an API.

Show inline field errors next to the relevant fields.

Do not rely only on a top-level error message.

Always show a loading state while submitting.

Disable the submit button while submitting.

Prevent duplicate submissions.

Show success feedback with a toast after completion.

Show error feedback with a toast when submission fails.

Use this flow for submit handlers:

```jsx
async function handleSubmit(event) {
  event.preventDefault();

  if (isSubmitting) {
    return;
  }

  const nextErrors = validateForm(values);
  setErrors(nextErrors);

  if (Object.keys(nextErrors).length > 0) {
    setToast({ type: "error", message: "Fix the highlighted fields." });
    return;
  }

  setIsSubmitting(true);

  try {
    const response = await fetch("/api/example", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(values),
    });

    if (!response.ok) {
      throw new Error("Request failed");
    }

    setToast({ type: "success", message: "Saved successfully." });
  } catch (error) {
    setToast({ type: "error", message: "Could not save changes." });
  } finally {
    setIsSubmitting(false);
  }
}
```

## Loading, Empty, And Error State Standard

Every UI that depends on data must handle loading state, empty state, and error state.

Do not render a blank area while data is loading.

Do not render an empty list without a clear empty state.

Do not swallow errors without visible feedback.

Errors should usually include a retry action.

Treat loading, empty, and error as distinct states:

```jsx
if (status === "loading") {
  return (
    <div className="rounded-md border border-gray-200 p-6 text-sm text-gray-600">
      Loading...
    </div>
  );
}

if (status === "error") {
  return (
    <div className="rounded-md border border-red-200 bg-red-50 p-6">
      <h3 className="text-sm font-medium text-red-900">Could not load data</h3>
      <p className="mt-1 text-sm text-red-700">
        The request failed. Try again before continuing.
      </p>
      <div className="mt-4">
        <Button onClick={loadData} loading={isRetrying}>
          Retry
        </Button>
      </div>
    </div>
  );
}

if (items.length === 0) {
  return (
    <div className="rounded-md border border-dashed border-gray-300 p-6 text-center">
      <h3 className="text-sm font-medium text-gray-900">No records found</h3>
      <p className="mt-1 text-sm text-gray-600">
        Add a record to get started.
      </p>
    </div>
  );
}
```

## File Upload Standard

Always show upload progress or a visible loading indicator during file upload.

Never let a file upload happen silently.

The user must always know the file is being uploaded, processed, completed, or failed.

Disable conflicting actions while upload or processing is active.

Show info feedback when upload starts.

Show success feedback when upload completes.

Show error feedback when upload fails.

Use native `XMLHttpRequest` when upload progress is required:

```jsx
function uploadWithProgress({ url, file, fieldName, onProgress }) {
  return new Promise((resolve, reject) => {
    const request = new XMLHttpRequest();
    const formData = new FormData();

    formData.append(fieldName, file);

    request.upload.addEventListener("progress", (event) => {
      if (!event.lengthComputable) {
        return;
      }

      onProgress(Math.round((event.loaded / event.total) * 100));
    });

    request.addEventListener("load", () => {
      if (request.status >= 200 && request.status < 300) {
        resolve(request.responseText);
        return;
      }

      reject(new Error("Upload failed"));
    });

    request.addEventListener("error", () => reject(new Error("Upload failed")));
    request.addEventListener("abort", () => reject(new Error("Upload cancelled")));

    request.open("POST", url);
    request.send(formData);
  });
}
```

Render upload progress without inline styles:

```jsx
<div className="space-y-2">
  <progress
    value={uploadProgress}
    max="100"
    className="h-2 w-full overflow-hidden rounded-full"
  >
    {uploadProgress}%
  </progress>
  <p className="text-sm text-gray-600">{uploadProgress}% uploaded</p>
</div>
```

If exact progress is not available, show a visible loading indicator and clear status text.

## Component Standard

Build reusable components for repeated UI and behavior.

Never duplicate logic across files when it belongs in a shared component or utility.

Keep components small, practical, and easy to read.

Do not invent abstractions before there is real repetition or shared behavior.

## Review Checklist

Before finishing any frontend task, check:

- Buttons use the reusable `Button` component.
- Async buttons show loading and are disabled.
- Inputs use the reusable `Input` component.
- Required inputs show `*`.
- Forms validate before API calls.
- Field errors are inline.
- Submit buttons disable while loading.
- User actions show success, error, or info feedback.
- File uploads show progress or loading.
- Data-backed UIs handle loading, empty, and error states.
- No new library was added unless the user explicitly asked for it.
- Styling uses Tailwind utilities only.
