# Loading Empty Error States

## Problem
Without this skill, AI agents render blank screens, empty lists without context, and errors that disappear into the console.

## Instruction
Every data-backed UI must explicitly handle loading, empty, and error states. Show a visible loading state while data is being fetched, a clear empty state when no records exist, and a clear error state with a retry path when loading fails.

## Example Prompt
Create a project list that fetches projects from `/api/projects`.

## Before (without this skill)

```jsx
import { useEffect, useState } from "react";

export default function ProjectList() {
  const [projects, setProjects] = useState([]);

  useEffect(() => {
    fetch("/api/projects")
      .then((response) => response.json())
      .then((data) => setProjects(data.projects));
  }, []);

  return (
    <div className="grid gap-3">
      {projects.map((project) => (
        <div key={project.id} className="rounded-md border border-gray-200 p-4">
          <h3 className="font-medium text-gray-900">{project.name}</h3>
          <p className="text-sm text-gray-600">{project.description}</p>
        </div>
      ))}
    </div>
  );
}
```

## After (with this skill)

```jsx
import { useEffect, useState } from "react";

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

function LoadingState() {
  return (
    <div className="rounded-md border border-gray-200 p-6 text-sm text-gray-600">
      Loading projects...
    </div>
  );
}

function EmptyState() {
  return (
    <div className="rounded-md border border-dashed border-gray-300 p-6 text-center">
      <h3 className="text-sm font-medium text-gray-900">No projects yet</h3>
      <p className="mt-1 text-sm text-gray-600">
        Create your first project to start tracking work.
      </p>
    </div>
  );
}

function ErrorState({ onRetry, retrying }) {
  return (
    <div className="rounded-md border border-red-200 bg-red-50 p-6">
      <h3 className="text-sm font-medium text-red-900">Could not load projects</h3>
      <p className="mt-1 text-sm text-red-700">
        The request failed. Try again before continuing.
      </p>
      <div className="mt-4">
        <Button onClick={onRetry} loading={retrying}>
          Retry
        </Button>
      </div>
    </div>
  );
}

export default function ProjectList() {
  const [projects, setProjects] = useState([]);
  const [status, setStatus] = useState("loading");
  const [isRetrying, setIsRetrying] = useState(false);

  async function loadProjects({ retry = false } = {}) {
    if (retry) {
      setIsRetrying(true);
    } else {
      setStatus("loading");
    }

    try {
      const response = await fetch("/api/projects");

      if (!response.ok) {
        throw new Error("Failed to load projects");
      }

      const data = await response.json();
      setProjects(data.projects || []);
      setStatus("success");
    } catch (error) {
      setStatus("error");
    } finally {
      setIsRetrying(false);
    }
  }

  useEffect(() => {
    loadProjects();
  }, []);

  if (status === "loading") {
    return <LoadingState />;
  }

  if (status === "error") {
    return (
      <ErrorState
        onRetry={() => loadProjects({ retry: true })}
        retrying={isRetrying}
      />
    );
  }

  if (projects.length === 0) {
    return <EmptyState />;
  }

  return (
    <div className="grid gap-3">
      {projects.map((project) => (
        <div key={project.id} className="rounded-md border border-gray-200 p-4">
          <h3 className="font-medium text-gray-900">{project.name}</h3>
          <p className="text-sm text-gray-600">{project.description}</p>
        </div>
      ))}
    </div>
  );
}
```

## Notes

Loading is a real state, not an empty array.

An empty array is not the same as loading.

Errors should be visible to the user and should usually include a retry action.

Do not rely only on `console.error`.

Every async data view needs all three states: loading, empty, and error.

Use small shared state components when several screens need the same loading, empty, or error layout.
