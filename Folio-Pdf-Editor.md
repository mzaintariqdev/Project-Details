# Folio — PDF Editor

Merge, reorder, and trim PDF pages entirely in your browser. Upload multiple
PDFs, browse every page in a sidebar, drop pages you don't want, reorder
what's left, and export the result as one PDF with the name you choose.
Nothing leaves your machine — there's no backend and no upload.


## Live Demo: https://folio-pdf-editor.netlify.app/

## Video

[screen-capture (13).webm](https://github.com/user-attachments/assets/1d7b26c1-f2dc-401a-9888-d954394e11b1)


## Features

- **Multi-file upload** — drag & drop or browse, add more files at any time.
- **Page-level control** — every page from every file shows up as its own
  thumbnail; remove it, reorder it, or preview it full-size.
- **Client-side merge** — pages are copied straight from the original files
  into a new PDF using [pdf-lib](https://pdf-lib.js.org/); nothing is
  altered until you export.
- **File validation** — checks extension, MIME type, size limit, and the
  actual `%PDF-` file signature, so a renamed non-PDF is caught before it
  ever reaches the PDF engine.
- **Visible error handling** — invalid or corrupted files produce a
  dismissible message per file instead of failing silently or crashing the
  page.
- **Responsive layout** — a side-by-side sidebar + viewer on desktop
  collapses to a horizontal page strip above the viewer on narrow screens.

## Tech stack

| Concern              | Library                                          |
|-----------------------|---------------------------------------------------|
| UI                    | React 18 + Vite                                    |
| Page rendering        | [pdf.js](https://mozilla.github.io/pdf.js/) (thumbnails, in a web worker) |
| PDF creation/merging  | [pdf-lib](https://pdf-lib.js.org/)                 |
| Linting / formatting  | ESLint (flat config) + Prettier                    |

## Project structure

```
src/
├── main.jsx                     # React entry point
├── App.jsx                      # Top-level layout & wiring
├── App.css                      # App-shell layout (flex containers, breakpoints)
│
├── components/                  # One folder per component: JSX + its own CSS Module
│   ├── AppHeader/                 top bar: brand + "Add PDF files"
│   ├── ErrorBanner/                dismissible list of file/processing errors
│   ├── UploadZone/                 drag-and-drop empty state
│   ├── PageSidebar/                scrollable list/strip of page thumbnails
│   ├── PageThumbnail/              a single page row/card (select, move, remove)
│   ├── PageViewer/                 large preview of the selected page
│   ├── ExportBar/                  output filename + "Create merged PDF"
│   └── Spinner/                    small inline loading indicator
│
├── hooks/
│   └── usePdfEditor.js           # All editor state & actions (pages, errors,
│                                  # add/remove/reorder, merge+download)
│
├── utils/
│   ├── pdfEngine.js              # pdf.js rendering + pdf-lib merging + download
│   ├── validateFile.js           # extension/MIME/size/magic-byte checks
│   └── formatBytes.js            # human-readable file sizes
│
├── constants/
│   └── config.js                 # size limits, accepted types, thumbnail scale
│
└── styles/
    └── tokens.css                 # design tokens (color, type, spacing) + resets + .btn
```

**Why this shape:** components only know how to render props and call
callbacks — all state and business logic lives in `usePdfEditor`, and all
PDF-specific work lives in `utils/pdfEngine.js`. That split means you can
change the UI without touching PDF logic, swap the PDF engine without
touching any component, and unit-test `utils/` and `hooks/` without a DOM.

## Getting started

```bash
npm install
npm run dev
```

Open the URL Vite prints (usually `http://localhost:5173`).

### Other scripts

```bash
npm run build     # production build -> dist/
npm run preview   # serve the production build locally
npm run lint      # ESLint
npm run format    # Prettier, writes changes in place
```

### Deploying

`npm run build` produces a static `dist/` folder — no server-side code to
run. Drop it on Netlify, Vercel, GitHub Pages, S3/CloudFront, or any static
host.

## How validation and errors work

`utils/validateFile.js` runs three checks, cheapest first, on every
selected/dropped file:

1. **Extension & declared MIME type** — must look like a PDF.
2. **Size** — rejects empty files and anything over `MAX_FILE_SIZE_MB`
   (see `constants/config.js`).
3. **Magic bytes** — reads the first 5 bytes and checks for the `%PDF-`
   signature, so a `.txt` file renamed to `.pdf` is still caught.

Anything that fails validation, or that pdf.js can't open (corrupted or
password-protected), produces a message in `usePdfEditor`'s `errors` array
instead of throwing — `ErrorBanner` renders each one with its own dismiss
button, and other valid files in the same batch still get processed
normally.

## Extending this project

A few natural next steps, and where they'd live:

- **Page rotation** — add a `rotate` field to each page's state in
  `usePdfEditor`, apply it visually with a CSS `transform` in
  `PageThumbnail`/`PageViewer`, and call pdf-lib's `page.setRotation()` in
  `pdfEngine.mergePdf` before adding the page.
- **Drag-to-reorder** — replace the ↑/↓ buttons in `PageThumbnail` with a
  drag library (e.g. `@dnd-kit/core`); the reorder logic in
  `usePdfEditor.movePage` already works on a plain array, so only the
  interaction layer changes.
- **Split a PDF into separate files** — add a new function in
  `pdfEngine.js` that builds one `PDFDocument` per selected page (instead of
  one merged document) and downloads each as a zip via a library like
  `jszip`.
- **Persisting between sessions** — since there's no backend, you could add
  `localStorage`/`IndexedDB` to remember an in-progress session; keep it in
  a new hook (e.g. `usePersistedPages`) rather than inside `usePdfEditor`
  so the core logic stays storage-agnostic.
- **Undo/redo** — `usePdfEditor` already centralizes every mutation
  (`addFiles`, `removePage`, `movePage`); wrapping its `setPages` calls with
  a small history stack would give you undo/redo with minimal changes
  elsewhere.
- **Automated tests** — `utils/validateFile.js` and `utils/pdfEngine.js`
  have no DOM/React dependencies, making them good first candidates for
  unit tests with [Vitest](https://vitest.dev/) (`npm install -D vitest`).

## Browser support & limits

- Requires a modern browser with Web Worker and `crypto.randomUUID`
  support (all current Chrome/Firefox/Safari/Edge).
- Since everything runs client-side, there's no server-imposed file-size
  cap, but very large PDFs (hundreds of pages, large scans) are limited by
  the browser's available memory rather than a server's.
- `constants/config.js: THUMB_SCALE` controls thumbnail sharpness vs.
  memory usage — raise it for crisper previews at the cost of more memory
  per page.
