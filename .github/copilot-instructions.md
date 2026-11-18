# Copilot instructions

SpaceQuiz is a single self-contained file, `index.html` (inline CSS + JS). There is no build, package manager, test suite, linter, or server. Do not add dependencies or split it into multiple files unless asked.

## Run / verify

- Open `index.html` directly (`file:///` URL), e.g. in the integrated browser canvas. Reload after edits.
- Verification is done in the browser by driving the page: call `document.dispatchEvent(new KeyboardEvent('keydown', {key: '2'}))` and click `#go`, `#next`, `#again`, then inspect `#pts`, `#fill`, `#final`, `#emoji`, `#title`.
- Avoid `setTimeout`/`sleep` in injected scripts (they timed out in the browser canvas); run the loop synchronously. Computed colors read right after a class change reflect the 0.2s transition start, so verify color via screenshot.

## Architecture

- Three `<section>`s (`#start`, `#quiz`, `#result`) inside one `.card`; `show(id)` toggles the `.hidden` class. State is module-level `i` (question index), `score`, `locked` (blocks double answers).
- Questions live in the `QUESTIONS` array as `[question, [4 options], correctIndex, factText]`. The result emoji/title comes from the descending-threshold `tiers` array in `finish()` (first tier with `score >= threshold` wins).
- Flow: `render()` builds option buttons → `answer(idx)` applies `.correct`/`.wrong`/`.dim`, updates score, progress bar, fact, full-screen `#flash` → `#next` calls `render()` or `finish()`.

## Conventions

- Design requirements: 10 questions, progress bar, green for correct and red for wrong with animation, emoji reaction on the result screen, narrow centered card (max-width 440px), sans-serif body at 15px (keep within 14–16px), "pepper" palette (charcoal-brown backgrounds with paprika/saffron accents) defined as CSS variables in `:root`. Use those variables rather than hard-coded colors.
- Keyboard support is required: keys 1–4 answer, Up/Down move option focus, focus moves to the first option on each question, to `#next` after answering, and to `#again` on the result screen. Preserve this when changing the flow.
- Keep a visible `:focus-visible` outline and `aria-live` on `#fact`.
