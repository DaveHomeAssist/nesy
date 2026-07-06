# NeSy

A long-form interactive research brief on **neuro-symbolic AI (NeSy)** in 2026 — architectures, evidence, and the LLM reliability shift — styled as an editorial "Veritas Canvas" intelligence report, with an interactive D3.js knowledge graph and a companion glossary.

This is a static content site: no backend, no build step, no framework. Everything is a self-contained HTML file with inline CSS/JS.

## What's here

| Path | What it is |
|---|---|
| `index.html` / `NeSy.html` | The main brief — two identical copies of the same ~1,700-line page (see Conventions below) |
| `NeSy-glossary.html` | Companion glossary — 61 NeSy-related terms with a sticky alphabetical index |
| `nesy-ai-2026-combo-light.html` | A lighter variant of the brief with no D3.js dependency and no interactive knowledge graph |
| `favicon.svg` | Site favicon |
| `vercel.json` | Deployment config — enables Vercel "clean URLs" (`/NeSy` instead of `/NeSy.html`) |
| `nesy-vercel-preview-feature-analysis-2026-03-25.md` | Internal feature audit of the interactive build (graph explorer, evidence table cross-links, tabs, progress bar, responsive/mobile behavior, accessibility) |

The main brief includes: an interactive D3 force-directed knowledge graph (architecture families, systems, concepts, papers), a filterable graph explorer with a node detail panel, an evidence table cross-linked to graph nodes, tabbed sections, a scroll-based reading-progress bar, and mobile-specific patterns (bottom sheet for graph detail, floating section pills).

## How to run

No install or build required. Either:

- Open `index.html` directly in a browser, or
- Serve the folder locally, e.g. `npx serve .` or `python -m http.server`, or
- Deploy to [Vercel](https://vercel.com) — `vercel.json` is already configured with `cleanUrls: true`, so `NeSy.html` resolves at `/NeSy`.

## Conventions

- **`index.html` and `NeSy.html` are intentionally identical.** With Vercel's clean URLs enabled, `NeSy.html` serves the canonical `/NeSy` path, while `index.html` covers the site root. They must be kept in sync manually — the feature-analysis doc flags this duplication (along with per-page duplicated CSS in the glossary) as a known content-drift risk, not yet consolidated into a shared template/stylesheet.
- The D3.js library is loaded from a CDN (cdnjs) with a pinned Subresource Integrity (SRI) hash on both `index.html` and `NeSy.html` — added after the initial commit as a security hardening pass.
