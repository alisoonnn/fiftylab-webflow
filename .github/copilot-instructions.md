## Quick orientation for AI coding agents

This is a small static site project (Webflow-export-like structure) using plain HTML + jQuery + GSAP and a Gulp-based build pipeline. Key paths:

- Source: `src/` (HTML, `js/`, `scss/`, `img/`, `fonts/`).
- SCSS entry: `src/scss/style.scss` — it imports `variables`, `mobile`, `desktop`.
- JS: `src/js/index.js`, `src/js/desktop.js` (DOM-heavy, jQuery + GSAP). Order matters: `index.js` defines globals like `mm` used by `desktop.js`.
- Build: `gulpfile.js` produces `dist/` (CSS/JS/images/fonts). `package.json` also contains `npm start` which runs `parcel index.js` but the canonical build/watch is `gulp`.

Important conventions and patterns (refer to files):

- Asset naming: HTML in `src/*.html` references unminified `.css`/`.js` and image extensions like `.jpg/.png`. The Gulp `replaceText()` task (see `gulpfile.js`) rewrites these to `.min.css`, `.min.js`, and `.webp` when files are copied to `dist/`. When adding new assets, keep source references in `src/` unminified — Gulp will produce and rewrite to minified names.

- SCSS layout: `src/scss/style.scss` imports partials. Edit `_mobile.scss` or `_desktop.scss` for breakpoint-specific styles. The Gulp `compileSass` task outputs `dist/css/style.min.css`.

- JS loading & globals: code relies on global ordering and DOM-ready (`$(document).ready`). `index.js` registers GSAP plugins and creates `mm` (matchMedia). Ensure `index.js` is loaded before `desktop.js` in HTML to avoid runtime errors.

- Webflow markers: the code checks classes like `w-dyn-bind-empty` and data attributes like `data-page` on `<body>` to determine behavior (e.g., active nav). Treat these markers as authoritative when transforming or testing pages.

Developer workflows (discoverable commands):

- Install deps: `npm install` (uses packages listed in `package.json`).
- Build & watch (recommended): run `gulp` from project root — the default task copies assets, compiles/minifies CSS and JS once, converts images to WebP, then watches SCSS only. Note: JS/HTML edits do not trigger the watcher; re-run `gulp` to rebuild after changes to JS or HTML.
- Quick dev server: `npm start` runs `parcel index.js` (present but not integrated with Gulp tasks). Use only if you purposely switch to a Parcel workflow.

Integration and gotchas:

- Dependencies: project uses jQuery and GSAP in source files but they are not listed as runtime scripts in `package.json`; they are expected to be provided by the HTML (CDN or vendor files). If you add bundling, add these libs explicitly or adjust HTML.
- Image handling: `convertToWebp` creates `.webp` files from JPG/PNG. `replaceText()` rewrites image references in `src/*.html` to `.webp` during dist copy — so do not manually rename image references in HTML before running Gulp.
- Fonts: `fonts()` copies OTF/WOFF/WOFF2 from `src/fonts` to `dist/fonts`.

Safety and minimal test guidance:

- There are no test runners configured. Before committing JS changes, open the corresponding `src/*.html` page in a browser (or serve `dist/` after `gulp`) to validate runtime behavior (menu toggles, lineup filters, GSAP ScrollTriggers).

Files to inspect for examples:
- `gulpfile.js` — build steps and replacement rules (primary source of truth for output names).
- `src/scss/style.scss` + `_mobile.scss` + `_desktop.scss` — how styles are organized and imported.
- `src/js/index.js`, `src/js/desktop.js` — how DOM is selected, global variables, and ordering expectations.

If you want me to expand or adjust any section (build matrix, add example snippets for PRs, or add commands for a CI job), tell me which area to expand.
