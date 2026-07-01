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

[To be completed in a later phase]

### Steps to Reproduce

[To be completed in a later phase]

### Reproduction Evidence

[To be completed in a later phase]

---

## Solution Approach

### Analysis

[To be completed in a later phase]

### Proposed Solution

[To be completed in a later phase]

### Implementation Plan

[To be completed in a later phase]

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
