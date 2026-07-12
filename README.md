# Token Foundry

A single-file, self-contained web app for building an accessible design token system — with **WCAG 2.1 AA contrast baked into the export**, not checked as an afterthought.

**▶ Live app: <https://nataschatruongmeck-stack.github.io/nagw_2026/>**

Type or paste your colors, type ramp, spacing, and component dimensions; watch the WCAG verdict for every color pair update live; then download a standards-compliant `tokens.json` you can drop into any codebase.

---

## What it does

Token Foundry turns a palette and a set of design decisions into a **W3C Design Tokens (DTCG)** file, structured in two layers per accessibility best practice:

- **`color.raw`** — the full palette, named by scale position (`blue-600`, `tint-lavender`). Reference material only.
- **`color.action` / `status` / `surface`** — semantic *pairs* that alias into the raw layer (`{color.raw.blue-600}`) and ship with a `$extensions."meck.contrast"` block recording each pair's measured WCAG ratio and the success criteria it passes. Components consume this layer only.

The accessibility audit lives *inside* the token file, so it can never drift out of sync with the colors.

## Features

- **Every token category** — colors (raw palette + semantic action/status/surface/link/focus pairs), typography (desktop + mobile ramps), spacing, radii, border widths, shadows, motion (durations + easing), breakpoints, z-index, opacity, and component dimensions (buttons, card, input).
- **Live WCAG 2.1 contrast** — an interactive matrix over the raw palette plus a per-pair PASS / large-text-only / FAIL verdict on every semantic pair, computed with the exact WCAG relative-luminance formula. Fills below 3:1 on white are automatically flagged as needing a border (SC 1.4.11).
- **Three downloads:**
  - `*.tokens.json` — the DTCG token file (shadows and easing exported as DTCG composite objects; the original CSS string is preserved under `$extensions["foundry.css"]`).
  - `style-dictionary.config.json` — a ready-to-run [Style Dictionary](https://styledictionary.com) v4 config that compiles the tokens to CSS custom properties, SCSS, and JS/TS, keeping the raw→semantic aliasing.
  - `instructions.md` — a tool-agnostic guide: the derived accessibility rules, how to build with Style Dictionary, and a tiny plain-JS resolver for anyone reading the JSON directly.
- **Full round-trip** — **Import JSON** reloads a previously exported file (or the original hand-authored `tokens.json`) so you can edit and re-export. Re-exporting an unchanged file is byte-identical.
- **Persistence & safety** — debounced autosave to `localStorage`, a restore-your-last-session banner on reload, an unsaved-changes warning before you close the tab, and undo on every delete and reset.
- **Honest font preview** — pick a Google Font from the list to preview it live, or type any self-hosted/brand font. The tool checks whether the font is *actually* available and tells you plainly when specimens are showing a fallback instead. Fonts are exported as a proper stack (`["Roboto", "system-ui", "sans-serif"]`).
- **Name validation** — warns on duplicate token names (which silently collide as JSON keys) and non-standard names, and blocks characters that break DTCG reference syntax.

## Accessibility

The tool practices what it exports. The interface itself meets WCAG 2.1 AA: all text and non-text (control borders, focus rings) contrast is verified, keyboard focus is always visible and never lost on navigation, controls are labeled, and live status changes — contrast verdicts, undo toasts, validation errors — are announced to screen readers via `aria-live` (SC 4.1.3).

## Using the exported tokens

**With Style Dictionary (recommended):**

```bash
npm install --save-dev style-dictionary
# place your <name>.tokens.json and style-dictionary.config.json in the project root
npx style-dictionary build
# -> build/css/tokens.css, build/scss, build/js
```

**Reading the JSON directly** — any language can parse it. To resolve a semantic pair, follow its reference:

```js
const t = require("./your.tokens.json");
const deref = v => typeof v === "string" && v.startsWith("{")
  ? v.slice(1, -1).split(".").reduce((o, k) => o[k], t).$value : v;

deref(t.color.action.primary.bg.$value); // -> "#2058CA"
```

The token only *names* the font — your site is responsible for loading it (self-hosted `@font-face` recommended for production; the Google Fonts link in the builder is a preview convenience only).

## Running it

Nothing to install or build. Either:

- Open the **[live app](https://nataschatruongmeck-stack.github.io/nagw_2026/)**, or
- Download `index.html` and open it in any modern browser.

It's a single HTML file with all CSS and JavaScript inline — no frameworks, no dependencies. Everything runs client-side and works offline; the only network request is the optional Google Fonts preview, which degrades gracefully when unavailable.

## License

See [LICENSE](LICENSE).
