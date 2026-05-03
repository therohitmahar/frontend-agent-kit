# Input Component

## Problem
Without this skill, AI agents scatter labels, required markers, error text, and input classes across forms until every field behaves slightly differently.

## Instruction
Always create and use a reusable `Input` component that includes its own `label`, shows a mandatory asterisk (`*`) when required, accepts a `type` prop, owns field-level rendering logic, supports inline errors, and keeps input styling consistent across the project.

## Example Prompt
Create an account settings form with name, email, and password fields.

## Before (without this skill)

```jsx
import { useState } from "react";

export default function AccountSettingsForm() {
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  return (
    <form className="space-y-4">
      <div>
        <p className="mb-1 text-sm text-gray-700">Name</p>
        <input
          value={name}
          onChange={(event) => setName(event.target.value)}
          className="w-full rounded border px-3 py-2"
        />
      </div>

      <div>
        <label className="block text-sm font-medium text-gray-700">Email</label>
        <input
          value={email}
          onChange={(event) => setEmail(event.target.value)}
          className="mt-1 w-full rounded-md border-gray-300 px-3 py-2"
        />
      </div>

      <div>
        <label>Password *</label>
        <input
          value={password}
          onChange={(event) => setPassword(event.target.value)}
          className="block w-full border p-2"
        />
      </div>
    </form>
  );
}
```

## After (with this skill)

```jsx
import { useId, useState } from "react";

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

export default function AccountSettingsForm() {
  const [values, setValues] = useState({
    name: "",
    email: "",
    password: "",
  });

  function updateField(field, value) {
    setValues((current) => ({
      ...current,
      [field]: value,
    }));
  }

  return (
    <form className="space-y-4">
      <Input
        label="Name"
        required
        value={values.name}
        onChange={(event) => updateField("name", event.target.value)}
      />

      <Input
        label="Email"
        required
        type="email"
        value={values.email}
        onChange={(event) => updateField("email", event.target.value)}
      />

      <Input
        label="Password"
        required
        type="password"
        value={values.password}
        onChange={(event) => updateField("password", event.target.value)}
      />
    </form>
  );
}
```

## Notes

The label belongs inside the component, not beside every usage.

The `type` prop should drive native input behavior.

Use `useId` when an explicit `id` is not passed.

Support `aria-invalid` and `aria-describedby` for inline errors.

Do not build separate one-off input components unless the field has genuinely different behavior.
