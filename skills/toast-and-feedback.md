# Toast And Feedback

## Problem
Without this skill, AI agents complete user actions silently and force users to infer whether the action worked.

## Instruction
Every user action must show feedback using a consistent success, error, or info toast pattern. Do not leave saves, deletes, uploads, sends, copies, or status changes silent. Prefer a small reusable toast component or utility written inside the project instead of installing a toast library.

## Example Prompt
Add a delete button to remove a customer from the list.

## Before (without this skill)

```jsx
import { useState } from "react";

export default function CustomerList({ initialCustomers }) {
  const [customers, setCustomers] = useState(initialCustomers);

  async function deleteCustomer(customerId) {
    await fetch(`/api/customers/${customerId}`, {
      method: "DELETE",
    });

    setCustomers((current) =>
      current.filter((customer) => customer.id !== customerId)
    );
  }

  return (
    <ul className="divide-y divide-gray-200">
      {customers.map((customer) => (
        <li key={customer.id} className="flex items-center justify-between py-3">
          <span className="text-sm font-medium text-gray-900">{customer.name}</span>
          <button
            type="button"
            onClick={() => deleteCustomer(customer.id)}
            className="rounded-md bg-red-600 px-3 py-2 text-sm text-white"
          >
            Delete
          </button>
        </li>
      ))}
    </ul>
  );
}
```

## After (with this skill)

```jsx
import { useState } from "react";

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

function Button({ children, loading = false, disabled = false, type = "button", variant = "primary", ...props }) {
  const variants = {
    primary: "bg-blue-600 text-white hover:bg-blue-700 focus:ring-blue-500",
    secondary: "border border-gray-300 bg-white text-gray-900 hover:bg-gray-50 focus:ring-gray-400",
    danger: "bg-red-600 text-white hover:bg-red-700 focus:ring-red-500",
  };

  return (
    <button
      type={type}
      disabled={disabled || loading}
      aria-busy={loading}
      className={`inline-flex items-center justify-center gap-2 rounded-md px-3 py-2 text-sm font-medium transition focus:outline-none focus:ring-2 focus:ring-offset-2 disabled:cursor-not-allowed disabled:opacity-60 ${variants[variant]}`}
      {...props}
    >
      {loading ? (
        <span className="h-4 w-4 animate-spin rounded-full border-2 border-current border-t-transparent" />
      ) : null}
      {children}
    </button>
  );
}

export default function CustomerList({ initialCustomers }) {
  const [customers, setCustomers] = useState(initialCustomers);
  const [toast, setToast] = useState(null);
  const [deletingId, setDeletingId] = useState(null);

  async function deleteCustomer(customerId) {
    if (deletingId) {
      return;
    }

    const customer = customers.find((item) => item.id === customerId);
    setDeletingId(customerId);
    setToast({ type: "info", message: "Deleting customer..." });

    try {
      const response = await fetch(`/api/customers/${customerId}`, {
        method: "DELETE",
      });

      if (!response.ok) {
        throw new Error("Delete failed");
      }

      setCustomers((current) =>
        current.filter((item) => item.id !== customerId)
      );
      setToast({
        type: "success",
        message: `${customer?.name || "Customer"} deleted.`,
      });
    } catch (error) {
      setToast({ type: "error", message: "Could not delete customer." });
    } finally {
      setDeletingId(null);
    }
  }

  return (
    <div className="space-y-4">
      <Toast toast={toast} onClose={() => setToast(null)} />

      <ul className="divide-y divide-gray-200">
        {customers.map((customer) => (
          <li key={customer.id} className="flex items-center justify-between py-3">
            <span className="text-sm font-medium text-gray-900">
              {customer.name}
            </span>
            <Button
              variant="danger"
              loading={deletingId === customer.id}
              disabled={Boolean(deletingId)}
              onClick={() => deleteCustomer(customer.id)}
            >
              Delete
            </Button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

## Notes

Use success to confirm completion.

Use error when an action fails.

Use info for in-progress or neutral feedback.

Feedback is required for writes, deletes, uploads, copies, sends, saves, and status changes.

Do not install a toast dependency unless the user explicitly asks for one.
