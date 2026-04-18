# NeSy Vercel Preview — Feature Analysis

**Date:** 2026-03-25
**Project:** nesy-vercel-preview
**Stack:** Static HTML + inline CSS + inline JS, D3.js (CDN), Vercel hosting with clean URLs

---

## Summary Table

| Feature | Status | Data Source / Persistence | Critical Gap |
|---|---|---|---|
| Long-form editorial layout (newspaper aesthetic) | Complete | Static HTML | None |
| Interactive D3 force-directed knowledge graph | Complete | Inline JS data, D3.js v7 | Graph data is hardcoded, not loadable |
| Graph explorer with detail panel | Complete | DOM state, no persistence | None |
| Tabbed content sections | Complete | JS tab switching with ARIA | None |
| Evidence table with cross-linking to graph | Complete | Static HTML table rows with `data-node-id` | None |
| Animated progress bars (metric visualization) | Complete | CSS transitions triggered by IntersectionObserver | None |
| Table of contents with scroll tracking | Complete | IntersectionObserver + sticky sidebar | None |
| Reading progress bar | Complete | Scroll event listener, fixed position bar | None |
| Glossary page with 61 terms | Complete | Static HTML, alphabetical with sticky letter index | None |
| Fade-in scroll animations | Complete | IntersectionObserver + CSS transitions | None |
| Responsive layout with mobile adaptations | Complete | CSS media queries, mobile bottom sheet | Graph panel hidden on mobile — detail goes to bottom sheet |
| Accessibility (focus-visible, reduced-motion, sr-only) | Complete | Native CSS/HTML | None |
| Duplicate page (index.html = NeSy.html) | Present | Both files identical | Redundant — one should redirect |

---

## Detailed Feature Analysis

### 1. Editorial Newspaper Layout

**Problem it solves:** Presents dense AI research content (neuro-symbolic architectures, evidence tables, governance frameworks) in a format that feels like reading a serious publication rather than a blog post.

**Implementation:** The design system uses 3 typefaces — Playfair Display (display headings), Libre Baskerville (body serif), and IBM Plex Mono (labels, tags, navigation). CSS custom properties define an editorial palette (`--ink`, `--paper`, `--cream`, `--rust`, `--gold`, `--slate`, `--steel`). A noise texture overlay via inline SVG filter (`body::before`) adds print-like grain. The masthead uses monospace uppercase navigation. Section scaffolding uses `.section-label` (mono uppercase) + `.section-h2` (Playfair Display) consistently throughout.

**Tradeoffs:** The newspaper aesthetic is visually distinctive but limits flexibility — adding interactive UI elements (forms, toggles, video) would clash with the static editorial feel. The fixed noise overlay costs a repaint layer on every scroll.

---

### 2. Interactive D3 Knowledge Graph

**Problem it solves:** Visualizes relationships between NeSy architecture families, systems, concepts, and research papers as a navigable force-directed graph.

**Implementation:** D3.js v7 loaded from CDN. Two graph instances exist: a hero mini-graph (`#hero-graph-svg`, ~380px height) for visual impact, and a full graph explorer (`.graph-explorer`) with a 480px canvas and a 260px side panel. Nodes are color-coded by type (`--n-family` rust, `--n-system` slate, `--n-concept` gold, `--n-paper` steel). Edges are typed (implements, extends, requires, cites) with corresponding colors. Force simulation uses `d3.forceSimulation` with link, charge, center, and collision forces. Nodes are draggable (`cursor: grab/grabbing`).

**Tradeoffs:** Graph data (nodes and edges) is hardcoded in inline JS, not loaded from a JSON file. The force layout is non-deterministic — graph appears slightly different each load. On mobile, the side panel is replaced by a bottom sheet (`.graph-bottom-sheet`) which requires a separate render path.

---

### 3. Graph Explorer with Detail Panel

**Problem it solves:** Lets readers click on any node in the knowledge graph to see its name, type, description, and connections.

**Implementation:** The explorer has a header with controls (`.graph-explorer-header`), a split body (canvas + 260px panel), and a legend footer. The panel has sections for node detail (name, type, description) and connections list. Filter buttons (`.ge-btn`) toggle node types on/off. The active node gets highlighted, and the panel populates with structured data. On mobile (< 900px), the side panel is hidden and a fixed bottom sheet (`.graph-bottom-sheet`) slides up with the same content.

**Tradeoffs:** The detail panel and bottom sheet are separate DOM structures that must be kept in sync. There is no search or text-based node lookup — discovery is visual only, which can be difficult with many nodes.

---

### 4. Evidence Table with Graph Cross-Linking

**Problem it solves:** Presents research evidence (papers, case studies, vendor claims) in a structured table that links directly to graph nodes.

**Implementation:** An HTML table (`.evidence-table`) with columns for system, evidence type, strength tag (`.tag-strong`, `.tag-proto`, `.tag-emerging`), and description. Rows have `data-node-id` attributes that, when clicked, highlight the corresponding node in the graph explorer. A small graph-link icon (`.evidence-graph-link`) provides a visual affordance for the connection.

**Tradeoffs:** The cross-linking only works within the same page — clicking a table row scrolls to and highlights a graph node, but this requires both the table and graph to be visible simultaneously. On mobile, the graph may be scrolled off screen.

---

### 5. Tabbed Content Sections

**Problem it solves:** Organizes dense content (e.g., implementation approaches, comparison matrices) into switchable views without page navigation.

**Implementation:** `.tab-nav` contains `.tab-btn` elements with click handlers that toggle `.active` class and show/hide `.tab-panel` elements via `[hidden]` attribute. The active tab gets a rust-colored bottom border via `::after` pseudo-element. ARIA attributes (`role="tablist"`, `role="tab"`, `role="tabpanel"`, `aria-selected`) provide screen reader semantics.

**Tradeoffs:** Tab state is not reflected in the URL hash, so deep-linking to a specific tab is not possible. Refreshing the page resets to the default tab.

---

### 6. Reading Progress Bar

**Problem it solves:** Shows readers how far through the long article they have scrolled.

**Implementation:** A fixed-position 3px bar (`#progress-bar`) at the top of the viewport with a rust-to-gold gradient. Width is computed from `scrollY / (documentHeight - viewportHeight)` on scroll events, with a 0.1s linear transition for smoothness.

**Tradeoffs:** The progress bar competes for the same top-of-viewport space as the masthead on some scroll positions. The scroll listener runs on every frame — no throttling observed (though modern browsers optimize this).

---

### 7. Glossary Page (61 Terms)

**Problem it solves:** Provides a reference companion to the main brief, defining key NeSy terms as used in the document.

**Implementation:** `NeSy-glossary.html` has its own masthead, hero, and footer matching the main brief's design. A sticky alphabetical letter index (`.letter-index`) at the top provides jump links. Terms are organized in `.letter-group` sections with `.term-entry` items displayed in a 2-column grid (14rem label + 1fr definition). Each term has colored tags (`.tag-arch`, `.tag-eval`, `.tag-gov`, `.tag-core`, `.tag-llm`). A scope note explains the glossary's narrowed definitions. Fade-in animations apply to each letter group.

**Tradeoffs:** The glossary is a separate HTML file with its own complete copy of the CSS (not shared). Any style changes must be applied to both NeSy.html and NeSy-glossary.html manually.

---

### 8. Duplicate Index File

**Problem it solves:** Vercel's `cleanUrls: true` config (vercel.json) serves NeSy.html as `/NeSy`. The index.html appears to be a copy of NeSy.html for the root URL.

**Implementation:** `index.html` and `NeSy.html` are identical files (same title, same content, same D3 graph). `vercel.json` enables clean URLs.

**Tradeoffs:** Two copies of the same ~600+ line file means any edit must be applied twice. A simple redirect in vercel.json or an HTML meta refresh would eliminate the duplication.

---

## Top 3 Priorities

1. **Eliminate the duplicate index.html/NeSy.html files.** Use a Vercel rewrite rule or make index.html a redirect to NeSy. This prevents content drift between the two files.

2. **Extract graph data into a loadable JSON file.** The knowledge graph nodes and edges are hardcoded in inline JS within each HTML file. A shared `nesy-graph.json` would make the graph data maintainable and potentially reusable across pages.

3. **Extract shared CSS into a common stylesheet.** The main brief and glossary duplicate the entire design system. A shared `nesy-theme.css` would cut maintenance in half and ensure visual consistency.
