# Button Component

## Problem
Without this skill, AI agents create one-off buttons that stay clickable during async actions and drift in style across the project.

## Instruction
Always create and use a reusable `Button` component with primary and secondary variants, a built-in loading state, a spinner, and automatic disabled behavior while loading. Never leave a button clickable during an async action, and never duplicate button styling across files when the shared component should be used.

## Example Prompt
Build a user invitation form with a submit button that sends the invite and shows progress while the request is running.

## Before (without this skill)

```jsx
import { useState } from "react";

export default function InviteUserForm() {
  const [email, setEmail] = useState("");

  async function handleInvite(event) {
    event.preventDefault();

    await fetch("/api/invitations", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ email }),
    });

    setEmail("");
  }

  return (
    <form onSubmit={handleInvite} className="space-y-4">
      <input
        value={email}
        onChange={(event) => setEmail(event.target.value)}
        className="w-full rounded-md border border-gray-300 px-3 py-2"
        placeholder="Email address"
      />

      <button
        type="submit"
        className="rounded-md bg-blue-600 px-4 py-2 text-sm font-medium text-white hover:bg-blue-700"
      >
        Send invite
      </button>
    </form>
  );
}
```

## After (with this skill)

```jsx
import { useState } from "react";

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
  const selectedVariant = variantClasses[variant] || variantClasses.primary;

  return (
    <button
      type={type}
      disabled={isDisabled}
      aria-busy={loading}
      className={`${baseClasses} ${selectedVariant} ${className}`}
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

export default function InviteUserForm() {
  const [email, setEmail] = useState("");
  const [isSubmitting, setIsSubmitting] = useState(false);

  async function handleInvite(event) {
    event.preventDefault();

    if (isSubmitting) {
      return;
    }

    setIsSubmitting(true);

    try {
      await fetch("/api/invitations", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ email }),
      });

      setEmail("");
    } finally {
      setIsSubmitting(false);
    }
  }

  return (
    <form onSubmit={handleInvite} className="space-y-4">
      <input
        value={email}
        onChange={(event) => setEmail(event.target.value)}
        className="w-full rounded-md border border-gray-300 px-3 py-2"
        placeholder="Email address"
      />

      <Button type="submit" loading={isSubmitting}>
        Send invite
      </Button>
    </form>
  );
}
```

## Notes

Use `type="button"` as the default to avoid accidental form submits.

Always derive the disabled state from `disabled || loading`.

Use `aria-busy` when loading is active.

The spinner should inherit the button text color so it works on both primary and secondary variants.

Do not add a button library for this. The component is small enough to write.

If the project already has a reusable button, improve that component instead of creating a second button abstraction.
