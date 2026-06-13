# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page marketing website for **VaultAI**, an offline-first personal-finance app with an on-device AI coach. 100% vanilla HTML/CSS/JS — **no framework, no build step, no dependencies, no package.json**. The site deliberately mirrors the app's "Aurora" design system.

## Commands

There is nothing to build or compile. To preview locally:

```bash
python3 -m http.server 8000   # then open http://localhost:8000
```

The site also works opened directly over `file://`. Deployment is GitHub Pages from the `master` branch root; `.nojekyll` is present so assets serve as-is.

## Architecture

Three top-level files do all the work:

- **`index.html`** — all markup, one page, ~9 `<section>` blocks in order: hero → trust strip → features → privacy band → screens (gallery) → how-it-works → download CTA → footer. Elements are wired to JS purely through `data-*` attributes (`data-aurora`, `data-insight`, `data-nav`, `data-gallery`, `data-count`/`data-suffix`) and the `.reveal` class — there are no IDs used as JS hooks. Section accent colors are passed via inline `style="--accent:#..."`.
- **`styles.css`** — all styling. The Aurora design system lives in `:root` as CSS custom properties (cosmic-void `--bg:#08080F`, `--violet:#7B61FF`, `--teal:#00D4C8`, `--rose:#FF5FA0`, `--amber:#FFB347`, the `--grad` brand gradient, fonts `--sans` Plus Jakarta Sans / `--mono` DM Mono). **Change colors and spacing through these tokens, not by hardcoding values in rules.** Responsive breakpoints are at the bottom (920 / 720 / 560px) followed by a `prefers-reduced-motion` block.
- **`app.js`** — all interaction, one IIFE in strict mode, organized as independent sub-IIFEs: the Aurora "neural field" canvas, the streaming-insight typewriter, scroll reveals, stat count-up, navbar scrolled-state, and gallery drag-to-scroll. No modules, no imports.

### Conventions that matter

- **`prefers-reduced-motion` is honored everywhere.** `app.js` reads it once into `reduceMotion` at the top; every animated feature has a static fallback (canvas draws one frame, typewriter shows full text, reveals/counts jump to final). When adding any animation, add the reduced-motion path too — both in JS and in the CSS media block.
- **The canvas color palette is duplicated**: `COLORS` in `app.js` (as `"r,g,b"` strings) must stay in sync with the violet/teal/rose tokens in `styles.css`.
- **`data-aurora` canvases self-configure**: the CTA canvas is detected via `classList.contains("cta-canvas")` to run a denser, centered field. Any new aurora canvas just needs the `data-aurora` attribute.
- Performance patterns are intentional: scroll/resize handlers are throttled via `requestAnimationFrame` or debounce timers, the render loop pauses on `visibilitychange`, and DPR is capped at 2.

### Assets

`assets/` holds brand SVGs (`logo.svg`, `favicon.svg`, custom store badges, `og-image.svg`/`.png`) and `assets/screenshots/` holds real dark-theme app captures used inside the CSS phone mockups. Screenshots already carry the cosmic background so they blend into the `.phone-screen` frames.

## Content notes

- Marketing copy is grounded in the app's real features (on-device LLMs — Gemma 2, Qwen 2.5, Phi, Llama, Mistral; a 9-country tax engine; scenario planning; opt-in cloud deep-analysis). It is positioned as educational information, **not financial advice** — keep that framing.
- The Google Play link uses real id `com.vaultai.app`; the **App Store URL is a placeholder** (iOS "coming soon") — swap in the real listing id when published.
