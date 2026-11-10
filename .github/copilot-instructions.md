# Copilot instructions

Space Quiz is one self-contained file, `index.html` (HTML, CSS and JS inline). There is no server, bundler, package manager, test suite or linter. Keep it that way: no build tools or npm dependencies.

## Running and verifying
- Open `index.html` directly (`file://`). The only external resource is the Google Fonts `<link>`; the page must still work offline with system-font fallbacks.
- Verify in the integrated browser canvas, and use its `evaluate_javascript` action for smoke tests. Quiz state (`i`, `score`, `locked`, `Q`) is in global scope, so tests can drive the UI via `.opt` buttons, `#next`, and `keydown` events.

## Architecture
- **Data:** `Q` is an array of `[emoji, question, options[], correctIndex, funFact]`. Adding or removing questions needs no other code changes, but the progress bar, "n of N" label and results text derive from `Q.length`, and the rank thresholds in `finish()` (>=9, >=6, >=3) assume 10 questions.
- **Flow:** `show()` renders a question, `answer()` locks input, updates score, feedback and bar, and focuses `#next`. `#next` calls `show()` or `finish()`, and `restart()` resets state. The `locked` flag prevents double answers (clicks and number keys).
- **Theming:** all colours are CSS variables on `:root`, overridden under `[data-theme="dark"]`, including the hex-grid background SVG (`--hex`). Initial theme comes from `prefers-color-scheme`; the toggle sets `data-theme` on `<html>`. Any new colour must be defined for both themes.

## Conventions
- Accessibility is maintained deliberately: option buttons are real `<button>`s, number keys 1-4 select answers, `#fb` is `aria-live="polite"`, focus moves explicitly (to `#next` after answering, to the first option on a new question or restart, to the results `<h2>` at the end), and `:focus-visible` has an outline. Keep focus management intact when changing the flow.
- Decorative elements (the mission-control `h1` LED and "LIVE" tag) are `aria-hidden`. The `h1` keeps its dark console look in both themes by design.
- Animations (`pop`, `shake`, `float`, confetti) are disabled via `prefers-reduced-motion`; new animations must be covered by that rule.
- Fonts: Space Grotesk (headings and numbers), Outfit (body), Share Tech Mono (title).
- The layout is a centred column with `max-width: 480px`.
