# Forms And Validation

## Problem
Without this skill, AI agents submit invalid data, show vague errors, and leave users guessing whether anything happened.

## Instruction
Always validate form data on the client before calling an API, show inline field errors, show loading while submitting, disable submit while loading, prevent duplicate submissions, and show success or error feedback with a toast after completion.

## Example Prompt
Build a contact form that submits name, email, and message to an API endpoint.

## Before (without this skill)

```jsx
import { useState } from "react";

export default function ContactForm() {
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");
  const [message, setMessage] = useState("");
  const [error, setError] = useState("");

  async function handleSubmit(event) {
    event.preventDefault();

    const response = await fetch("/api/contact", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({ name, email, message }),
    });

    if (!response.ok) {
      setError("Something went wrong");
    }
  }

  return (
    <form onSubmit={handleSubmit} className="space-y-4">
      {error ? <p className="text-sm text-red-600">{error}</p> : null}

      <input
        value={name}
        onChange={(event) => setName(event.target.value)}
        placeholder="Name"
        className="w-full rounded-md border px-3 py-2"
      />

      <input
        value={email}
        onChange={(event) => setEmail(event.target.value)}
        placeholder="Email"
        className="w-full rounded-md border px-3 py-2"
      />

      <textarea
        value={message}
        onChange={(event) => setMessage(event.target.value)}
        placeholder="Message"
        className="w-full rounded-md border px-3 py-2"
      />

      <button className="rounded-md bg-blue-600 px-4 py-2 text-white">
        Send
      </button>
    </form>
  );
}
```

## After (with this skill)

```jsx
import { useId, useState } from "react";

function Button({ children, loading = false, disabled = false, type = "button", ...props }) {
  return (
    <button
      type={type}
      disabled={disabled || loading}
      aria-busy={loading}
      className="inline-flex items-center justify-center gap-2 rounded-md bg-blue-600 px-4 py-2 text-sm font-medium text-white transition hover:bg-blue-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2 disabled:cursor-not-allowed disabled:opacity-60"
      {...props}
    >
      {loading ? (
        <span
          className="h-4 w-4 animate-spin rounded-full border-2 border-current border-t-transparent"
          aria-hidden="true"
        />
      ) : null}
      {children}
    </button>
  );
}

function Input({ label, error, required = false, type = "text", ...props }) {
  const id = useId();
  const errorId = error ? `${id}-error` : undefined;

  return (
    <div className="space-y-1">
      <label htmlFor={id} className="block text-sm font-medium text-gray-800">
        {label}
        {required ? <span className="ml-1 text-red-600">*</span> : null}
      </label>
      <input
        id={id}
        type={type}
        required={required}
        aria-invalid={error ? "true" : "false"}
        aria-describedby={errorId}
        className={`w-full rounded-md border px-3 py-2 text-sm outline-none focus:ring-2 ${
          error
            ? "border-red-500 focus:border-red-500 focus:ring-red-100"
            : "border-gray-300 focus:border-blue-500 focus:ring-blue-100"
        }`}
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

function Textarea({ label, error, required = false, ...props }) {
  const id = useId();
  const errorId = error ? `${id}-error` : undefined;

  return (
    <div className="space-y-1">
      <label htmlFor={id} className="block text-sm font-medium text-gray-800">
        {label}
        {required ? <span className="ml-1 text-red-600">*</span> : null}
      </label>
      <textarea
        id={id}
        required={required}
        aria-invalid={error ? "true" : "false"}
        aria-describedby={errorId}
        className={`min-h-32 w-full rounded-md border px-3 py-2 text-sm outline-none focus:ring-2 ${
          error
            ? "border-red-500 focus:border-red-500 focus:ring-red-100"
            : "border-gray-300 focus:border-blue-500 focus:ring-blue-100"
        }`}
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
      className={`rounded-md border px-4 py-3 text-sm ${classes[toast.type]}`}
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

function validateContact(values) {
  const errors = {};

  if (!values.name.trim()) {
    errors.name = "Name is required.";
  }

  if (!values.email.trim()) {
    errors.email = "Email is required.";
  } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(values.email)) {
    errors.email = "Enter a valid email address.";
  }

  if (!values.message.trim()) {
    errors.message = "Message is required.";
  }

  return errors;
}

export default function ContactForm() {
  const [values, setValues] = useState({
    name: "",
    email: "",
    message: "",
  });
  const [errors, setErrors] = useState({});
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [toast, setToast] = useState(null);

  function updateField(field, value) {
    setValues((current) => ({
      ...current,
      [field]: value,
    }));
    setErrors((current) => ({
      ...current,
      [field]: "",
    }));
  }

  async function handleSubmit(event) {
    event.preventDefault();

    if (isSubmitting) {
      return;
    }

    const nextErrors = validateContact(values);
    setErrors(nextErrors);

    if (Object.keys(nextErrors).length > 0) {
      setToast({ type: "error", message: "Fix the highlighted fields." });
      return;
    }

    setIsSubmitting(true);

    try {
      const response = await fetch("/api/contact", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(values),
      });

      if (!response.ok) {
        throw new Error("Request failed");
      }

      setValues({ name: "", email: "", message: "" });
      setToast({ type: "success", message: "Message sent." });
    } catch (error) {
      setToast({ type: "error", message: "Could not send your message." });
    } finally {
      setIsSubmitting(false);
    }
  }

  return (
    <div className="space-y-4">
      <Toast toast={toast} onClose={() => setToast(null)} />

      <form onSubmit={handleSubmit} className="space-y-4">
        <Input
          label="Name"
          required
          value={values.name}
          error={errors.name}
          onChange={(event) => updateField("name", event.target.value)}
        />

        <Input
          label="Email"
          required
          type="email"
          value={values.email}
          error={errors.email}
          onChange={(event) => updateField("email", event.target.value)}
        />

        <Textarea
          label="Message"
          required
          value={values.message}
          error={errors.message}
          onChange={(event) => updateField("message", event.target.value)}
        />

        <Button type="submit" loading={isSubmitting}>
          Send
        </Button>
      </form>
    </div>
  );
}
```

## Notes

Validation should run before the API call.

Inline errors should be field-specific.

The submit button must be disabled while the request is running.

Use a guard at the top of submit handlers to prevent duplicate submissions.

Show a toast for both success and failure.

If the project already has shared `Button`, `Input`, `Textarea`, or toast components, use those instead of copying the baseline components into the same file.
