# Copilot / AI agent instructions — lfwd

Keep changes targeted, preserve visual styling, and prefer small, reversible edits. This single-page site is a static HTML/CSS/JS deliverable — there is no build system, backend, or tests in the repository.

Quick facts
- Single entry: `index.html` contains full markup, CSS (inlined <style>), and small client JS at the file bottom.
- No package.json, no build, no server files.
- Fonts: Google Fonts are loaded from `fonts.googleapis.com` in the <head>.
- Visual theme: CSS variables defined on `:root` (e.g. `--gold`, `--blue`) — changing these updates the whole theme.

What matters (big-picture)
- This is a static landing page. Changes should preserve the visual layout and typographic scale. The CSS is structured into named sections (TOP BAR, NAV, HERO, VSL, etc.) — edit those regions only when necessary.
- Interactive behavior is minimal and lives inside the bottom `<script>` of `index.html`. Key interactive areas:
  - VSL (video-style landing) controls: elements by id `playBtn`, `vslThumb`, `vslTimer`, `vslProgress`, `vslCta`, `vslContainer`. The `startVSL()` function drives play state and progress.
  - FAQ toggles: `faqToggle(el)` is attached inline via `onclick` on `.faq-item` elements.
  - Scroll reveal: IntersectionObserver targets elements with class `reveal` and adds `visible` when in view.

Project-specific conventions and patterns
- Single-file authoring: HTML, CSS and JS are colocated. When modifying behavior prefer small, localized edits to the existing script rather than extracting into new modules unless the change is large and agreed on.
- CSS organization: Styles are grouped with section comments (e.g. "═ TOP BAR ═"). Follow that ordering when adding new rules.
- Theme via CSS variables: use/extend `:root` vars instead of hard-coded colors to keep consistency.
- Responsive breakpoints: a single media query at `@media (max-width: 900px)` is used; test responsive changes against that breakpoint.
- Utility-style classes in HTML: e.g. `.reveal`, `.text-center`, `.btn-main`, `.btn-main-ghost`. Reuse them to keep markup consistent.

Safe edit checklist for AI agents
- Read the entire `index.html` before making edits — many rules/IDs are interdependent.
- Preserve HTML ids used by JS: `playBtn`, `vslThumb`, `vslTimer`, `vslProgress`, `vslCta`, `vslContainer`. Renaming requires updating the inline script.
- If you change a CSS variable name, update all usages (`:root` → search across file). Prefer adding new variables instead of renaming existing ones.
- Keep inline SVG/data-URI backgrounds and Google Fonts intact unless you intentionally replace them with equivalent assets.
- Do not remove or change the IntersectionObserver behavior unless replacing with an equivalent approach — it's used widely for reveal animations.

Local dev & quick testing
- There is no build step. Open `index.html` directly in a browser for basic testing. For a local server (recommended for correct font loading and file URLs):

  - Python 3 built-in server (macOS zsh):

    ```bash
    # from repo root
    python3 -m http.server 8000
    # then open http://localhost:8000/
    ```

  - Node (if you prefer):

    ```bash
    npx serve . -l 8000
    ```

- Browser devtools: inspect CSS variables, network panel to confirm Google Fonts, and console for JS errors (watch for missing IDs if elements were changed).

Examples from the codebase to reference
- Theme variables: `:root { --blue: #3B82F6; --gold: var(--blue); ... }`
- VSL controls and timing constants: `const TOTAL_SECONDS = 47 * 60;` and `function startVSL()`.
- FAQ inline handler: `<div class="faq-item" onclick="faqToggle(this)">` and `function faqToggle(el) { ... }`.
- Reveal system: `document.querySelectorAll('.reveal').forEach(el => revealObs.observe(el));` plus `.reveal.visible` CSS.

When to propose structural refactors
- Small visual or content fixes: modify `index.html` directly.
- If adding JS features > ~60 lines or splitting concerns: propose extracting `scripts/` and `styles/` to separate files and provide a migration patch. Note: the repo has no bundler; extracted files must be simple, static references (e.g., `<link rel="stylesheet" href="styles/main.css">`, `<script src="scripts/main.js"></script>`).

Edge cases & gotchas
- Inline onclick attributes used for FAQ — converting to event listeners is fine but update markup and the inline attributes together.
- Some elements rely on inline `style` attributes (e.g., plan price font sizing) — moving them to CSS must preserve specificity and responsive behavior.
- Accessibility: there are no ARIA attributes or alt text for decorative elements. If you add accessibility improvements, do it incrementally and keep visual tests passing.

If you make a change
- Keep the diff minimal and explain the intent in the PR title/body (e.g., "fix: Make VSL timer resilient to missing playBtn" or "feat: extract VSL JS to scripts/vsl.js").
- Run a quick smoke test: start local server, open homepage, click VSL play, open FAQ items, and view console for errors.

Questions / missing info
- There are no tests or CI configuration. If you need preferred tooling (linting, formatting, build), ask the maintainers before adding config files.

— End of file —
