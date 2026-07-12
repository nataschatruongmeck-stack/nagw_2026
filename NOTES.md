# Token Foundry — Working Notes

Everything you need to run the design-token workflow, in one place.
Last updated: 2026-07-11.

---

## 1. Links

| What | Where |
|------|-------|
| **Live app** (use this) | https://nataschatruongmeck-stack.github.io/nagw_2026/ |
| **GitHub repo** (source + README) | https://github.com/nataschatruongmeck-stack/nagw_2026 |
| Local copy of the app | `Design Token System/token-foundry.html` |
| Build folder (Style Dictionary) | `Design Token System/token-build/` |

The live app and the local file are the **same tool**. Their autosaves are separate
(the browser saves per-location), so pick one and stick with it, or use Download /
Import JSON to move work between them.

---

## 2. What the tool does

Token Foundry is a single self-contained web page that builds an accessible design
token system and exports it as a **W3C DTCG `tokens.json`**. Colors are two layers:

- `color.raw` — the full palette, named by scale position (`blue-600`). Reference only.
- `color.action` / `status` / `surface` — semantic *pairs* that alias into raw
  (`{color.raw.blue-600}`) and carry their live **WCAG 2.1 AA contrast verdict** in the file.

It also covers typography, spacing, radii, borders, shadows, motion, breakpoints,
z-index, opacity, and component dimensions — and exports three files:

- `*.tokens.json` — the tokens (DTCG format).
- `style-dictionary.config.json` — config for the Style Dictionary build step.
- `instructions.md` — a plain-English guide + derived accessibility rules.

---

## 3. How to run the APP

Nothing to install — it's a web page.

- **Easiest:** open the live link above in any browser.
- **Local:** double-click `token-foundry.html`, or run
  `open "/Users/Natascha.Truong/Design Token System/token-foundry.html"`

---

## 4. How to run STYLE DICTIONARY (turn tokens.json into CSS)

Style Dictionary is a command-line build tool (already installed in `token-build/`).
It converts the exported `tokens.json` into CSS variables, SCSS, and JS/TS.

### The everyday workflow
1. In Token Foundry, edit values, then click **Download tokens.json** and
   **Style Dictionary config**.
2. Save **both** files into `Design Token System/token-build/`, replacing the old ones.
3. **Double-click `build.command`** in that folder.
   - It rebuilds everything and opens `build/css/` with the fresh CSS.
   - First time only, macOS may say "unverified developer" → right-click → **Open** once.

### Or from the terminal
```bash
cd "/Users/Natascha.Truong/Design Token System/token-build"
npx style-dictionary build --config style-dictionary.config.json
```

### What you get
```
token-build/build/
├── css/tokens.css        ← CSS custom properties (use these)
├── scss/_tokens.scss
├── js/tokens.js  +  tokens.d.ts
└── json/tokens.flat.json
```
Semantic tokens keep their link to raw, e.g.
`--color-action-primary-bg: var(--color-raw-blue-600);`

### Using the CSS in a site
```css
@import "./build/css/tokens.css";

.btn-primary {
  background: var(--color-action-primary-bg);
  color:      var(--color-action-primary-text);
  padding:    var(--spacing-4);
  border-radius: var(--radius-md);
}
```
Never hand-edit files in `build/` — change values in the app, re-export, rebuild.

---

## 5. Gotchas (things that bit us)

- **The `--config` flag is REQUIRED.** `npx style-dictionary build` alone fails with
  *"unable to find config file"* because the CLI only auto-looks for `config.json`.
  Always use `--config style-dictionary.config.json`. (`build.command` already does.)
- **The two files must actually be IN the folder before building.** If you see
  `ENOENT … config file` or `zsh: no matches found: *.tokens.json`, it means the
  download step was skipped or the files are somewhere else (usually still in Downloads).
- **Moving the folder is fine** — it's self-contained. Move the *whole* folder (incl.
  `node_modules`), not just the script. On a *different* computer, run `npm install` once.
- **Don't keep duplicate copies** of `token-build` in two places — they drift apart.

---

## 6. Prerequisites (already satisfied on this Mac)

- Node.js `v25.9.0`, npm `11.13.0` — check with `node --version`.
- Style Dictionary is installed inside `token-build/node_modules/`.
