# File Upload UX

## Problem
Without this skill, AI agents let files upload silently with no progress, no processing state, and no clear failure feedback.

## Instruction
Always show upload progress or a visible loading indicator during file upload, prevent conflicting actions while upload or processing is active, show success feedback when complete, show error feedback on failure, and never let a file upload happen silently.

## Example Prompt
Build a profile photo uploader that sends the selected file to `/api/profile/photo`.

## Before (without this skill)

```jsx
import { useState } from "react";

export default function ProfilePhotoUploader() {
  const [file, setFile] = useState(null);

  async function uploadPhoto() {
    const formData = new FormData();
    formData.append("photo", file);

    await fetch("/api/profile/photo", {
      method: "POST",
      body: formData,
    });
  }

  return (
    <div className="space-y-4">
      <input type="file" onChange={(event) => setFile(event.target.files[0])} />
      <button
        type="button"
        onClick={uploadPhoto}
        className="rounded-md bg-blue-600 px-4 py-2 text-white"
      >
        Upload
      </button>
    </div>
  );
}
```

## After (with this skill)

```jsx
import { useState } from "react";

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
        <span className="h-4 w-4 animate-spin rounded-full border-2 border-current border-t-transparent" />
      ) : null}
      {children}
    </button>
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
    <div className={`rounded-md border px-4 py-3 text-sm ${classes[toast.type]}`}>
      <div className="flex items-start justify-between gap-4">
        <p>{toast.message}</p>
        <button type="button" onClick={onClose} className="font-medium">
          Dismiss
        </button>
      </div>
    </div>
  );
}

function uploadWithProgress({ url, file, fieldName, onProgress }) {
  return new Promise((resolve, reject) => {
    const request = new XMLHttpRequest();
    const formData = new FormData();

    formData.append(fieldName, file);

    request.upload.addEventListener("progress", (event) => {
      if (!event.lengthComputable) {
        return;
      }

      const percent = Math.round((event.loaded / event.total) * 100);
      onProgress(percent);
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

export default function ProfilePhotoUploader() {
  const [file, setFile] = useState(null);
  const [uploadProgress, setUploadProgress] = useState(0);
  const [isUploading, setIsUploading] = useState(false);
  const [toast, setToast] = useState(null);

  async function uploadPhoto() {
    if (!file || isUploading) {
      return;
    }

    setIsUploading(true);
    setUploadProgress(0);
    setToast({ type: "info", message: "Uploading photo..." });

    try {
      await uploadWithProgress({
        url: "/api/profile/photo",
        file,
        fieldName: "photo",
        onProgress: setUploadProgress,
      });

      setToast({ type: "success", message: "Profile photo uploaded." });
      setFile(null);
      setUploadProgress(100);
    } catch (error) {
      setToast({ type: "error", message: "Could not upload profile photo." });
    } finally {
      setIsUploading(false);
    }
  }

  return (
    <div className="space-y-4">
      <Toast toast={toast} onClose={() => setToast(null)} />

      <label className="block space-y-1">
        <span className="text-sm font-medium text-gray-800">Profile photo</span>
        <input
          type="file"
          accept="image/*"
          disabled={isUploading}
          onChange={(event) => setFile(event.target.files?.[0] || null)}
          className="block w-full text-sm text-gray-700 file:mr-4 file:rounded-md file:border-0 file:bg-gray-100 file:px-4 file:py-2 file:text-sm file:font-medium file:text-gray-800 hover:file:bg-gray-200 disabled:cursor-not-allowed disabled:opacity-60"
        />
      </label>

      {isUploading ? (
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
      ) : null}

      <Button onClick={uploadPhoto} loading={isUploading} disabled={!file}>
        Upload photo
      </Button>
    </div>
  );
}
```

## Notes

Use `XMLHttpRequest` when native upload progress is required because `fetch` does not expose upload progress in the same direct way.

If exact progress is not available, show an obvious loading indicator and clear status text.

Disable file selection and submit actions while uploading when changing the file would conflict with the active request.

Show info feedback when upload starts, success feedback when it completes, and error feedback when it fails.

Do not install an upload library unless the user explicitly asks for one.
