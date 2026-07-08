# Contribution 2: Add File Upload example page + tests

**Contribution Number:** 2  
**Student:** Raymond Lin  
**Issue:** [#218 — [good first issue] Add File Upload example page + tests](https://github.com/BRIKEV/twd/issues/218)  
**Status:** Phase I Complete

---

I had to switch to a new issue from my previous 2nd issue since my old issue was taken/closed. So I'm starting a new issue

## Why I Chose This Issue

The main reason I chose this issue is that I already have experience with the languages it uses, and I wanted to work on smaller repositories where I can easily learn more about the project and contribute greatly to it. This one is a React/TypeScript frontend feature paired with tests, which sits right in my comfort zone, and TWD ("Test While Developing") is a small enough codebase that I can actually understand how the whole thing fits together rather than getting lost in a huge project. TWD is also interesting to me because of what it does differently. It runs tests in the real browser against the actual dev server instead of jsdom or a separate E2E stack, and adding an example page is a great way to learn how that in-browser test runner and its `.twd.test.ts` files actually work against the real DOM.

The issue is also explicitly labeled "good first issue," which tells me it has a concrete definition of done: a new `FileUpload.tsx` page wired into `routes.tsx` and the app menu, plus a `file-upload.twd.test.ts` with two test cases (a successful upload showing the filename, and error handling for a non-image file). It even points to a reference implementation (`ComboboxSelect.tsx`) and steers contributors toward Testing Library's `screenDom` over the legacy `twd.get()` API. I'm less interested in "easy" and more interested in "well-defined." Between the maintainer's setup instructions, the acceptance criteria, and the reference page, this one gives me a clear path from clone to PR while still teaching me something new.

---

## Understanding the Issue

### Problem Description

The `twd-test-app` example app is a gallery of real-world DOM patterns, where each page demonstrates one interaction (a combobox, blur validation, responsive layout, screen queries, and so on) and is paired with a matching `.twd.test.ts` file that exercises it in the browser. One common pattern is missing from that gallery: file input handling through `<input type="file">`. Because there is no example page or test for it, contributors have no reference for how to drive a file upload with TWD, and the file-input path is not covered by the example test suite.

### Expected Behavior

The example app should include a new `FileUpload.tsx` page with a single file input that displays the selected filename and rejects non-image files with a visible error message. The page should be registered as a route and reachable from the app, following the same structure as the existing `ComboboxSelect.tsx` page. Alongside it, a `file-upload.twd.test.ts` file should provide two passing test cases: a happy path that uploads an image with `userEvent.upload(input, file)` and asserts the filename becomes visible, and an invalid-type case that uploads a non-image file and asserts an error appears. All element queries should use Testing Library's `screenDom` (for example `screenDom.getByLabelText`, `screenDom.getByText`, `screenDom.getByRole("alert")`, or `screenDom.getByTestId("upload-error")`) rather than the legacy `twd.get()` API.

### Current Behavior

No file-upload example exists. The `examples/twd-test-app/src/pages/` directory contains pages like `ComboboxSelect.tsx`, `BlurValidation.tsx`, and `ScreenQueries.tsx`, but no `FileUpload.tsx`. The `examples/twd-test-app/src/twd-tests/` directory contains tests like `combobox-select.twd.test.ts`, but no `file-upload.twd.test.ts`. `routes.tsx` registers routes such as `/combobox-select` and `/blur-validation`, but has no `/file-upload` route. As a result, the `<input type="file">` pattern is neither demonstrated in the app nor validated by the test runner.

### Affected Components

- **`examples/twd-test-app/src/pages/FileUpload.tsx`** (new) — the page with the file input, selected-filename display, and non-image error messaging.
- **`examples/twd-test-app/src/routes.tsx`** — add the import and a `/file-upload` route entry, matching the existing pattern.
- **App menu / navigation** — add a link so the new page is reachable in the running app.
- **`examples/twd-test-app/src/twd-tests/file-upload.twd.test.ts`** (new) — the two `screenDom`-based test cases (successful upload and invalid file type).
- **Reference files (not modified, used as models):** `pages/ComboboxSelect.tsx` for page structure, `twd-tests/combobox-select.twd.test.ts` for test structure, and `twd-tests/screen-queries.twd.test.ts` for query patterns.

---

## Reproduction Process

### Environment Setup

TWD is a plain Node/Vite project — no container setup required. Following `CONTRIBUTING.md`'s "Testing with twd-test-app (React / dev mode)" section:

**Prerequisites**
- Node.js and npm
- Git

**Platform Note (Windows):** I did this work on Windows, and ran into issues because several commands in `CONTRIBUTING.md` and in this doc (e.g. `grep -rn ...`) are Linux/Mac shell commands that aren't available in a plain Windows `cmd.exe`/PowerShell session. To follow the steps below as written, use **Git Bash** (installed alongside Git for Windows) or **WSL** instead of PowerShell/Command Prompt. Alternatively, PowerShell equivalents exist for most commands (e.g. `Select-String` instead of `grep`), but the commands in this README assume a Unix-like shell.

**Steps**

1. Fork and clone the repo, then create a feature branch (per `CONTRIBUTING.md`, never commit directly to `main`):
   ```bash
   git clone https://github.com/YOUR_USERNAME/twd.git
   cd twd
   git checkout -b feat/file-upload-example
   ```

2. From the repo root, install dependencies, build the library, and sync the mock service worker into the example apps:
   ```bash
   npm install
   npm run build
   npm run copy:mock-sw
   ```

3. Install and start the example app that this issue targets:
   ```bash
   cd examples/twd-test-app
   npm install
   npm run dev
   ```

4. Open the app in the browser (`http://localhost:5173` by default). The **TWD sidebar** renders on the right — this is where `.twd.test.ts` files are discovered and run live against the real DOM (not jsdom), which is the detail that makes this project different from a typical Jest/RTL setup.

`examples/twd-test-app` imports TWD directly from `../../../../src`, so once the dev server is running, further edits to either the library or the example pages are picked up via HMR without re-running `npm run build`.

### Steps to Reproduce

This issue is a missing-example "good first issue," not a bug, so there's nothing broken to trigger — "reproducing" it means confirming the gap the issue describes actually exists in the current codebase before building anything:

1. With the dev server running, navigate to `http://localhost:5173/file-upload`. This falls through to the catch-all `{ path: "*", element: <div>404 Not Found</div> }` entry at the bottom of `routes.tsx`, confirming no `/file-upload` route is registered yet.

2. Search the repo for any existing file-input pattern:
   ```bash
   grep -rn "userEvent.upload\|type=\"file\"\|FileList\|accept=" src examples
   ```
   This returns **zero matches** anywhere in `src/` or `examples/` — confirming the issue's claim that `<input type="file">` is a "real-world pattern not currently covered" by any page or test in the project.

3. Open the TWD sidebar in the browser — no `file-upload` suite appears, since the sidebar discovers suites from the `.twd.test.ts` files present in `examples/twd-test-app/src/twd-tests/`, and `file-upload.twd.test.ts` doesn't exist yet.

4. Confirm the reference pattern the issue points to: `examples/twd-test-app/src/pages/ComboboxSelect.tsx` and `examples/twd-test-app/src/twd-tests/combobox-select.twd.test.ts` do exist, are paired one-to-one (one page ↔ one `.twd.test.ts` file), and are wired into `routes.tsx` as a single import + route object. `examples/twd-test-app/src/twd-tests/screen-queries.twd.test.ts` also exists and demonstrates the `screenDom.getByLabelText` / `getByRole` / `getByTestId` query styles the issue asks the new test to use instead of the legacy `twd.get()` API. This confirms both the gap and the exact pattern the fix needs to follow.

### Reproduction Evidence

- The 404 at `/file-upload` and the empty grep result are the concrete evidence that the page, route, and test genuinely don't exist yet — this isn't a case of "the feature exists but is buggy," it's a clean gap in the example gallery.
- Directory listing confirms the gap precisely:
  - `examples/twd-test-app/src/pages/` contains `ComboboxSelect.tsx`, `BlurValidation.tsx`, `ScreenQueries.tsx`, `Responsive.tsx`, `Contact.tsx`, `Contacts.tsx`, `LoadShows.tsx`, `MockComponent.tsx`, and the root `App/` — no `FileUpload.tsx`.
  - `examples/twd-test-app/src/twd-tests/` contains `combobox-select.twd.test.ts` and `screen-queries.twd.test.ts` (among others) — no `file-upload.twd.test.ts`.
  - `routes.tsx` registers ten page routes plus the catch-all — no `/file-upload` entry.
- One extra finding relevant to the issue's "add a link from the App menu" instruction: there is no shared navigation/menu component anywhere in `examples/twd-test-app/src`. The root `App.tsx` (`/`) is just the default Vite + React starter page (logo, counter, a `Jokes` component) — it doesn't render links to `/combobox-select`, `/screen-queries`, or any other example page. Every existing example page is reachable only because it's registered in `routes.tsx`; there's no menu list to add an entry to. This matters for the Solution Approach below.
- Screenshots of the 404 page and the TWD sidebar with the missing suite will be captured and attached here once the dev server is running locally for the implementation phase.

---

## Solution Approach

### Analysis

There's no bug or root cause to trace here — this is a coverage gap in the example gallery, not broken behavior. Each existing page in `examples/twd-test-app/src/pages/` demonstrates exactly one DOM interaction pattern paired 1:1 with a matching `*.twd.test.ts` file: `ComboboxSelect.tsx` covers keyboard-driven dropdown selection, `BlurValidation.tsx` covers blur-triggered validation, `ScreenQueries.tsx` covers the full `screenDom` query surface. `<input type="file">` is a common real-world pattern (avatar uploads, document attachments) that none of these cover, so there's currently no working reference for how TWD's in-browser test runner drives `userEvent.upload()` against a real `<input type="file">`, or for asserting on the resulting `FileList`/filename — which is exactly the gap the issue is asking to close.

The other analysis finding is about the "reachable from the App menu" line in the issue's scope. As confirmed in Reproduction Evidence, no shared nav/menu component exists anywhere in `examples/twd-test-app/src` — the root `App.tsx` doesn't link to any of the other nine example pages, including the ones this issue names as references. So the existing, established pattern for "reachability" in this app is simply: a page component + a route entry in `routes.tsx`, visited directly by URL. Treating the new page any differently (e.g., inventing a new nav component solely for this PR) would be inconsistent with every sibling page and out of scope for a single "good first issue."

### Proposed Solution

Build `FileUpload.tsx` structurally modeled on `ComboboxSelect.tsx` — a self-contained functional component using `useState`, inline styles, and the same `maxWidth: 480` centered wrapper other example pages use — paired with a `file-upload.twd.test.ts` modeled on `combobox-select.twd.test.ts`'s structure and `screen-queries.twd.test.ts`'s `screenDom` query style.

**Page (`FileUpload.tsx`):**
- A single `<input type="file" accept="image/*" id="file-upload-input">` wired to a `<label htmlFor="file-upload-input">`, so the input is queryable via `screenDom.getByLabelText(...)` — the same label/input association `screen-queries.twd.test.ts` already proves out for `Search:` and `Choose an option:`.
- `onChange` reads `e.target.files?.[0]`. If `file.type` doesn't start with `"image/"`, set an `error` state and clear any previously displayed filename; otherwise set a `filename` state and clear any previous error.
- The filename renders as visible text (queryable via `screenDom.getByText(...)`, matching the `data-testid="selected-country"` display pattern in `ComboboxSelect.tsx`).
- The error renders in an element with `role="alert"` and `data-testid="upload-error"` — both query targets are named explicitly in the issue body, so exposing both costs nothing and gives the test either option.

**Routing:** add `import FileUpload from './pages/FileUpload';` and a `{ path: '/file-upload', Component: FileUpload }` entry to `routes.tsx`, in the same position/shape as the existing `combobox-select` entry, placed before the catch-all `*` route. No separate menu/nav edit is needed — per the Analysis above, this matches how every other example page is already "reachable" in this app.

**Test (`file-upload.twd.test.ts`):** import `{ twd, screenDom, userEvent, expect }` and `{ describe, it }`, matching the existing test files' import shape, then two cases:
- **Happy path:** `await twd.visit("/file-upload")` → get the input via `screenDom.getByLabelText(...)` → build an image `File` (e.g. `new File(["(binary)"], "photo.png", { type: "image/png" })`) → `await userEvent.upload(input, file)` → assert the filename appears via `screenDom.getByText("photo.png")` and `twd.should(el, "be.visible")`.
- **Invalid type:** same visit/lookup → build a non-image `File` (e.g. `new File(["data"], "notes.txt", { type: "text/plain" })`) → upload it → assert the error is visible via `screenDom.getByRole("alert")` (or `screenDom.getByTestId("upload-error")`), and that no filename text was rendered.

Every query in the test uses `screenDom`, per the issue's explicit steer away from the legacy `twd.get()` API — consistent with `screen-queries.twd.test.ts`, which is the file the issue names as the query-pattern reference.

### Implementation Plan

1. **Branch:** confirm work happens on `feat/file-upload-example`, created off a fresh fork/clone of `BRIKEV/twd` (per `CONTRIBUTING.md`, never commit directly to `main`).

2. **Build the page — `examples/twd-test-app/src/pages/FileUpload.tsx` (new):**
   - `useState<string | null>` for `filename`, `useState<string | null>` for `error`.
   - `<label htmlFor="file-upload-input">Upload an image:</label>` + `<input type="file" accept="image/*" id="file-upload-input" onChange={handleChange} />`.
   - `handleChange` reads `e.target.files?.[0]`; if missing, no-op; if `!file.type.startsWith("image/")`, set `error` to a message and clear `filename`; otherwise set `filename` to `file.name` and clear `error`.
   - Render the filename as visible text when set (e.g. `<p data-testid="uploaded-filename">{filename}</p>`), and the error when set inside `<p role="alert" data-testid="upload-error">{error}</p>`.
   - Wrap in the same `maxWidth: 480, margin: '2rem auto', padding: '2rem'` container style `ComboboxSelect.tsx` uses, with an `<h2>File Upload</h2>` heading, for visual consistency with sibling pages.

3. **Register the route — `examples/twd-test-app/src/routes.tsx`:**
   - Add `import FileUpload from './pages/FileUpload';` alongside the other page imports.
   - Add `{ path: '/file-upload', Component: FileUpload }` immediately after the `/combobox-select` entry and before the catch-all `*` route.
   - No separate nav/menu component edit — per the Analysis above, every existing example page is reachable the same way (route entry + direct URL), so this new page follows that established precedent rather than introducing a new menu.

4. **Write the test — `examples/twd-test-app/src/twd-tests/file-upload.twd.test.ts` (new):**
   - `import { twd, screenDom, userEvent, expect } from "../../../../src";` and `import { describe, it } from "../../../../src/runner";`, matching `combobox-select.twd.test.ts`.
   - `describe("File Upload", ...)` with two `it` cases:
     - Happy path: `await twd.visit("/file-upload")` → `const input = screenDom.getByLabelText(/upload an image/i)` → `const file = new File(["(binary)"], "photo.png", { type: "image/png" })` → `await userEvent.upload(input, file)` → assert via `screenDom.getByText("photo.png")` + `twd.should(el, "be.visible")`.
     - Invalid type: same visit/lookup → `const file = new File(["data"], "notes.txt", { type: "text/plain" })` → `await userEvent.upload(input, file)` → assert via `screenDom.getByRole("alert")` (or `screenDom.getByTestId("upload-error")`) is visible, and `screenDom.queryByTestId("uploaded-filename")` is `null`.

5. **Verify locally:** with `npm run dev` running in `examples/twd-test-app`, open the TWD sidebar in the browser, confirm the new `File Upload` suite appears and both tests run green, and manually exercise the page once by hand (upload an image, then a non-image file) to confirm the UI matches what the tests assert.

6. **Commit in small, independently-correct steps** (per `CONTRIBUTING.md`): one commit for the page + route registration, one commit for the test file — each should compile and pass on its own.

7. **Open the PR** using `.github/pull_request_template.md`, referencing `Fixes #218`, checking the "New feature" type, filling in Test Details with the two cases above, and attaching the required screenshots: the green TWD sidebar and the new page in the browser.

---

## Testing Strategy

### Unit Tests

[To be completed in a later phase]

### Integration Tests

[To be completed in a later phase]

### Manual Testing

[To be completed in a later phase]

---

## Implementation Notes

### Development Environment Setup Issues (Windows/WSL)

Before writing any `FileUpload.tsx` code, I hit a chain of local environment problems just getting `examples/twd-test-app`'s dev server running per the `CONTRIBUTING.md` steps. None of this touched feature code — it was all tooling/environment — but it ate significant time and is worth recording since it's a direct consequence of `CONTRIBUTING.md`'s Unix-shell assumption (see the Platform Note above) going deeper than just `grep`.

1. **`localhost:5173` returned a blank page.**
   Root cause: a *different* `vite` dev server was already bound to port 5173 — one started with `npm run dev` from the **repo root** (`twd/`) rather than from `examples/twd-test-app/`. The root repo is the library itself and has no `index.html`, so that stray server 404'd on every request, which rendered blank in the browser. Meanwhile, when I started `twd-test-app`'s own dev server, Vite silently detected the port was taken and fell back to `5174` without saying anything obvious about *why* 5173 wasn't it. Fix: identify and stop the stray root-level process (`netstat -ano` → `Get-Process -Id`), then restart `twd-test-app`'s server, which then bound to 5173 correctly.

2. **`Failed to resolve import "twd-js/bundled" from "virtual:twd/init"`.**
   `examples/twd-test-app/vite.config.ts` aliases `twd-js/bundled` to `../../dist/bundled.es.js`, but that file didn't exist in `dist/` — only `dist/bundled.d.ts` (type declarations) was present. Root cause: the repo root's `build` script is:
   ```
   vite build && vite build -c vite.sw.config.js && NODE_ENV=production vite build -c vite.bundle.config.ts && cp src/cli/installsw.js dist/cli.js
   ```
   On Windows, `npm run build` executes scripts via `cmd.exe` regardless of which terminal you typed the command in (even Git Bash), and `cmd.exe` doesn't understand the POSIX `VAR=value command` prefix syntax. So the `NODE_ENV=production vite build -c vite.bundle.config.ts` step — the one that actually produces `dist/bundled.*` — failed immediately with `'NODE_ENV' is not recognized as an internal or external command`, and everything chained after it with `&&` (including the `cp` that produces `dist/cli.js`) silently never ran. The build reported success up to that point with no obvious top-level failure, which made this easy to miss.
   Two fixes were considered: (a) add `cross-env` as a devDependency and rewrite the script to be cross-platform, or (b) do all TWD development from a POSIX shell (WSL) instead, leaving the shared script untouched. I chose (b) to avoid changing shared build tooling for a docs/example contribution — see the WSL issues below.

3. **WSL's `node -v` reported `v12.22.9`** (Ubuntu's ancient apt-installed system Node) even though `nvm` had modern versions (`v20.15.0`, `v22.4.0`) installed. Root cause: `~/.bashrc`'s `nvm` loading lines were placed *after* the standard Ubuntu guard `case $- in *i*) ;; *) return;; esac`, which returns immediately for any non-interactive shell. So any non-interactive invocation of `bash` (e.g. `bash -lc '...'`, which is how tooling/scripts and even some editor integrations invoke commands) never sourced `nvm` and silently fell through to the ancient system `node` on `PATH`. Fix: installed the current Node LTS via `nvm install --lts` (`v24.18.0`), set it as `nvm alias default`, and added a duplicate `NVM_DIR`/`nvm.sh` sourcing block *before* the interactive-shell guard in `~/.bashrc` so both interactive and non-interactive shells resolve the same `node`. (Backed up the original as `~/.bashrc.bak` first.)

4. **`node_modules` installed via Windows `npm` don't work under WSL.** After switching to WSL, `npm run build` failed with `Cannot find module '@rolldown/binding-linux-x64-gnu'` — the `node_modules` on disk had Windows-native optional-dependency binaries (`@rolldown/binding-win32-x64-msvc`, etc.) because they'd been installed by Windows `npm`, not Linux `npm`. Fix: `rm -rf node_modules && npm install` from *inside* WSL, for both the repo root and `examples/twd-test-app`, so the correct Linux-native binaries got pulled instead.

5. **`rm -rf node_modules` failed with an I/O error / Access Denied on one specific `.node` binary.** This turned out to be a file lock: an earlier Windows-side `node.exe` process (a `twd-test-app` dev server I'd started directly on Windows while debugging issue #1, and never stopped) still had that native binary mapped/open. Killing the stray Windows `node.exe` processes (`Stop-Process`) released the lock and let the delete succeed.

6. **A `twd-test-app` dev server started from WSL via a background command (`(npm run dev &)`) died silently** once the invoking command finished, even though it printed a few lines of output first. Plain `&` backgrounding wasn't enough to fully detach the process from the invoking shell session. Fix: use `setsid nohup npm run dev > log 2>&1 < /dev/null & disown`, which fully detaches the process so it survives after the launching command returns. Confirmed the detached server was actually reachable (`curl` returning `HTTP 200`) both from inside WSL and from Windows `localhost:5173` (WSL2 auto-forwards ports).

**Net effect:** all of the above was resolved by moving local development for this contribution into WSL — Node updated to `v24.18.0` via `nvm`, `node_modules` reinstalled Linux-native at both the repo root and `examples/twd-test-app`, a full `npm run build` + `npm run copy:mock-sw` run completed successfully (producing the previously-missing `dist/bundled.es.js` and `dist/cli.js`), and `twd-test-app`'s dev server now runs cleanly on `http://localhost:5173`. No repo files were changed to reach this state (a `cross-env` fix to the shared `build` script was considered but deliberately not applied, to keep this contribution scoped to the example page/tests).

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
