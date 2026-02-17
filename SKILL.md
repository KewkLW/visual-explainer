---
name: stitch-visual-explainer
description: "Stitch companion: analyze exported screen designs to generate interactive flow diagrams, architecture maps, and prioritized agent task lists. Also generates beautiful HTML pages for any technical diagram, code review, or data table. Based on visual-explainer by @nicobailon."
license: MIT
compatibility: Requires a browser to view generated HTML files. Optional surf-cli for AI image generation.
metadata:
  author: KewkLW
  upstream: nicobailon/visual-explainer
  version: "0.3.0"
---

# Stitch Visual Explainer

> Based on [visual-explainer](https://github.com/nicobailon/visual-explainer) by [@nicobailon](https://github.com/nicobailon). This fork adds Stitch export analysis and agent task generation.

Generate self-contained HTML files for technical diagrams, visualizations, and data tables. When pointed at a Stitch export, analyze all screens and produce flow diagrams with prioritized agent task lists. Always open the result in the browser. Never fall back to ASCII art when this skill is loaded.

**Proactive table rendering.** When you're about to present tabular data as an ASCII box-drawing table in the terminal (comparisons, audits, feature matrices, status reports, any structured rows/columns), generate an HTML page instead. The threshold: if the table has 4+ rows or 3+ columns, it belongs in the browser. Don't wait for the user to ask — render it as HTML automatically and tell them the file path. You can still include a brief text summary in the chat, but the table itself should be the HTML page.

## Stitch Export Analysis

When pointed at a Stitch export directory (a folder containing screen subdirectories, each with `code.html` and `screen.png`), follow this workflow:

### 1. Scan the Export

- List all subdirectories in the export folder
- Read `code.html` from key screens to understand: page titles, navigation patterns, UI components, design tokens (colors, fonts, Tailwind config), auth methods, data models
- Extract the bottom nav / tab bar structure to understand the app's primary navigation
- Count total screens

### 2. Classify Screens into Domains

Group screens by function. Common domain patterns:
- **Auth**: login, signup, onboarding, terms, password reset
- **Home / Dashboard**: main hub, overview, command center
- **Core Feature A**: the primary activity (workout, task creation, content editing, etc.)
- **Core Feature B**: secondary activity (nutrition, messaging, analytics, etc.)
- **Programs / Content**: libraries, catalogs, builders, detail views
- **Analytics / Stats**: trends, charts, reports, body stats
- **Recovery / Wellness**: breathing, meditation, sleep, recovery
- **Settings**: preferences, privacy, theme, account, data export/import
- **Monetization**: plans, upgrades, pricing, ad gates
- **Admin**: debug panels, moderation queues, internal tools
- **Placeholder**: "coming soon" screens

Assign each domain a distinct color for the flow diagrams.

### 3. Generate the Output

Produce a multi-section HTML page containing:

1. **KPI overview** — Total screens, domain count, nav tab count, coming-soon count
2. **Auth flow diagram** — Mermaid flowchart showing authentication paths
3. **Navigation structure** — Bottom nav tabs and what each opens
4. **Full system flow** — Complete Mermaid diagram of all screens with color-coded domain subgraphs
5. **Domain breakdown** — Card grid with every screen listed per domain, with folder names as `<code>` references
6. **Tech stack table** — Extracted from the HTML: framework, fonts, icons, colors, theme mode, layout patterns
7. **Primary user journey** — The core interaction loop as a Mermaid flowchart
8. **Prioritized agent task list** — Build roadmap (see Agent Task Lists below)

### 4. Match the App's Aesthetic

Read the Tailwind config and CSS from the screens to extract the app's design language (colors, fonts, effects). Use that same aesthetic for the diagram page itself — if the app is dark with amber accents, the diagram should be too.

## Agent Task Lists

When generating flow diagrams for apps or multi-screen systems, **always include a prioritized build task list**. This is the key output for agent workflows — it turns a visual analysis into actionable work.

**Table format:** Use a styled `<table>` with columns:
- **Priority** — Color-coded tag (P0–P6)
- **Task** — Bold title + `<small>` description with enough detail for an agent to start coding
- **Domain** — Color-coded tag matching the flow diagrams
- **Screens** — Count of screens this task covers
- **Dependencies** — What must be built first (reference other tasks)

**Priority tiers:**

| Tier | Color | Meaning | Examples |
|------|-------|---------|----------|
| P0 | Red | Foundation — nothing works without this | Design system, app shell, auth, core data models |
| P1 | Amber | Core loop — the main reason the app exists | Primary feature CRUD, the screen users spend most time on |
| P2 | Green | Retention — features that drive daily engagement | Secondary features, content creation tools |
| P3 | Blue | Depth — enriching existing domains | Advanced search, custom items, barcode scanning |
| P4 | Purple | Polish — analytics, settings, data portability | Trends, preferences, export/import |
| P5 | Orange | Nice-to-haves — wellness, monetization | Meditation, upgrade flows, ad gates |
| P6 | Grey | Admin & stubs — ship last | Debug panels, coming-soon placeholders |

**After the table**, include:
1. A **callout** explaining the build order rationale
2. A **KPI card row** summarizing each tier (task count + screen count)

**The task descriptions must be specific enough that an AI agent can start implementing from the task alone**, referencing the screen folder names and key UI elements found during analysis.

## Test Generation

When generating tests from a Stitch export analysis, derive test cases systematically from the same data used for flow diagrams and task lists. Every screen, flow edge, and P0–P1 task should map to at least one test.

### Test Derivation Algorithm

| Source Data | Test Prefix | What's Derived |
|-------------|-------------|----------------|
| Navigation flow edges | `NAV_xxx` | Click trigger element → verify destination screen loads (check heading, key element) |
| Auth flow paths | `AUTH_xxx` | Login/signup/magic-link → verify auth state change (redirect, session, user element) |
| Domain screens | `CMP_xxx` | Navigate to screen → verify key headings, buttons, inputs exist on the page |
| Interactive elements | `INT_xxx` | Toggle/click/select element → verify state change (class toggle, value update, visibility) |
| Forms | `FRM_xxx` | Empty/valid/invalid submit → verify validation messages and success states |
| `screen.png` files | `VIS_xxx` | Full-page screenshot comparison against Stitch baseline image |
| Screen structure | `A11Y_xxx` | Heading hierarchy (h1→h2→h3), label associations, ARIA roles, tab order |
| P0–P1 agent tasks | `E2E_xxx` | Full user journey from task acceptance criteria (multi-step, cross-screen) |

**Numbering:** Within each prefix, number sequentially starting at 001 (`NAV_001`, `NAV_002`, …).

**Priority mapping:** Tests inherit priority from their source — auth/nav foundation tests are P0, core feature tests are P1, deeper features P2–P3.

### Test Output Structure

Generate two outputs:

**1. Visual test suite** — An HTML page using the `test-suite.html` template aesthetic (electric indigo/lime "test lab" palette):
- Test summary KPIs with SVG coverage ring
- Coverage heatmap (screens × test density)
- Test dependency graph (Mermaid: AUTH→NAV→CMP→INT/FRM→E2E)
- Domain test sections (collapsible) with test case cards
- Playwright file map table
- Run instructions callout

Write to `~/.agent/diagrams/<app-name>-tests.html` and open in browser.

**2. Playwright test files** — Runnable `.spec.ts` files using Page Object Model:

```
~/.agent/tests/<app-name>/
├── playwright.config.ts          # baseURL, viewport, retries, screenshot on failure
├── tests/
│   ├── fixtures.ts               # authenticated page, test user, seed data helpers
│   ├── auth.spec.ts              # AUTH_xxx tests
│   ├── navigation.spec.ts        # NAV_xxx tests
│   ├── <domain>.spec.ts          # CMP_xxx, INT_xxx, FRM_xxx for each domain
│   ├── accessibility.spec.ts     # A11Y_xxx tests
│   ├── visual.spec.ts            # VIS_xxx screenshot comparisons
│   ├── e2e.spec.ts               # E2E_xxx full journey tests
│   └── pages/
│       └── <ScreenName>Page.ts   # Page Object per major screen
└── baselines/
    └── <screen>.png              # Copied from Stitch export screen.png files
```

**Page Object Model:** Each major screen gets a `Page.ts` class with:
- Locators for key elements (heading, buttons, inputs, nav items)
- Action methods (`login(email, password)`, `startWorkout()`, `addSet(weight, reps)`)
- Assertion helpers (`expectLoaded()`, `expectError(message)`)

**Visual baselines:** Copy `screen.png` files from the Stitch export into `baselines/` for `VIS_xxx` screenshot comparison tests.

### Test Suite Quality Check

Add to the quality check pass:
- **Screen coverage:** Every screen from the Stitch export has at least one `CMP_xxx` test
- **Flow coverage:** Every navigation edge in the flow diagram has a corresponding `NAV_xxx` test
- **Task coverage:** Every P0–P1 agent task has at least one `E2E_xxx` test
- **No orphans:** Every test references a real screen folder name from the export

## General Workflow

### 1. Think (5 seconds, not 5 minutes)

Before writing HTML, commit to a direction. Don't default to "dark theme with blue accents" every time.

**Who is looking?** A developer understanding a system? A PM seeing the big picture? A team reviewing a proposal? This shapes information density and visual complexity.

**What type of diagram?** Architecture, flowchart, sequence, data flow, schema/ER, state machine, mind map, data table, timeline, or dashboard. Each has distinct layout needs and rendering approaches (see Diagram Types below).

**What aesthetic?** Pick one and commit:
- Monochrome terminal (green/amber on black, monospace everything)
- Editorial (serif headlines, generous whitespace, muted palette)
- Blueprint (technical drawing feel, grid lines, precise)
- Neon dashboard (saturated accents on deep dark, glowing edges)
- Paper/ink (warm cream background, hand-drawn feel, sketchy borders)
- Hand-drawn / sketch (Mermaid `handDrawn` mode, wiggly lines, informal whiteboard feel)
- IDE-inspired (borrow a real color scheme: Dracula, Nord, Catppuccin, Solarized, Gruvbox, One Dark)
- Data-dense (small type, tight spacing, maximum information)
- Gradient mesh (bold gradients, glassmorphism, modern SaaS feel)

Vary the choice each time. If the last diagram was dark and technical, make the next one light and editorial. The swap test: if you replaced your styling with a generic dark theme and nobody would notice the difference, you haven't designed anything.

### 2. Structure

**Read the reference template** before generating. Don't memorize it — read it each time to absorb the patterns.
- For text-heavy architecture overviews (card content matters more than topology): read `./templates/architecture.html`
- For flowcharts, sequence diagrams, ER, state machines, mind maps: read `./templates/mermaid-flowchart.html`
- For data tables, comparisons, audits, feature matrices: read `./templates/data-table.html`

**For CSS/layout patterns and SVG connectors**, read `./references/css-patterns.md`.

**For pages with 4+ sections** (reviews, recaps, dashboards), also read `./references/responsive-nav.md` for section navigation with sticky sidebar TOC on desktop and horizontal scrollable bar on mobile.

**Choosing a rendering approach:**

| Diagram type | Approach | Why |
|---|---|---|
| Architecture (text-heavy) | CSS Grid cards + flow arrows | Rich card content (descriptions, code, tool lists) needs CSS control |
| Architecture (topology-focused) | **Mermaid** | Visible connections between components need automatic edge routing |
| Flowchart / pipeline | **Mermaid** | Automatic node positioning and edge routing; hand-drawn mode available |
| Sequence diagram | **Mermaid** | Lifelines, messages, and activation boxes need automatic layout |
| Data flow | **Mermaid** with edge labels | Connections and data descriptions need automatic edge routing |
| ER / schema diagram | **Mermaid** | Relationship lines between many entities need auto-routing |
| State machine | **Mermaid** | State transitions with labeled edges need automatic layout |
| Mind map | **Mermaid** | Hierarchical branching needs automatic positioning |
| Data table | HTML `<table>` | Semantic markup, accessibility, copy-paste behavior |
| Timeline | CSS (central line + cards) | Simple linear layout doesn't need a layout engine |
| Dashboard | CSS Grid + Chart.js | Card grid with embedded charts |

**Mermaid theming:** Always use `theme: 'base'` with custom `themeVariables` so colors match your page palette. Use `look: 'handDrawn'` for sketch aesthetic or `look: 'classic'` for clean lines. Use `layout: 'elk'` for complex graphs (requires the `@mermaid-js/layout-elk` package — see `./references/libraries.md` for the CDN import). Override Mermaid's SVG classes with CSS for pixel-perfect control. See `./references/libraries.md` for full theming guide.

**Mermaid interaction:** Add a fullscreen toggle button (top-right corner) to every `.mermaid-wrap` container. Mouse scroll directly zooms the diagram (no Ctrl/Cmd modifier needed). Click-and-drag pans when zoomed in. Escape key exits fullscreen. Max zoom: 20x in fullscreen, 8x inline. Do NOT use +/- zoom buttons — the fullscreen + scroll-to-zoom approach is cleaner and more intuitive.

**AI-generated illustrations (optional).** If [surf-cli](https://github.com/nicobailon/surf-cli) is available, you can generate images via Gemini and embed them in the page for creative, illustrative, explanatory, educational, or decorative purposes. Check availability with `which surf`. If available:

```bash
# Generate to a temp file (use --aspect-ratio for control)
surf gemini "descriptive prompt" --generate-image /tmp/ve-img.png --aspect-ratio 16:9

# Base64 encode for self-containment (macOS)
IMG=$(base64 -i /tmp/ve-img.png)
# Linux: IMG=$(base64 -w 0 /tmp/ve-img.png)

# Embed in HTML and clean up
# <img src="data:image/png;base64,${IMG}" alt="descriptive alt text">
rm /tmp/ve-img.png
```

See `./references/css-patterns.md` for image container styles (hero banners, inline illustrations, captions).

**When to use:** Hero banners that establish the page's visual tone. Conceptual illustrations for abstract systems that Mermaid can't express (physical infrastructure, user journeys, mental models). Educational diagrams that benefit from artistic or photorealistic rendering. Decorative accents that reinforce the aesthetic.

**When to skip:** Anything Mermaid or CSS handles well. Generic decoration that doesn't convey meaning. Data-heavy pages where images would distract. Always degrade gracefully — if surf isn't available, skip images without erroring. The page should stand on its own with CSS and typography alone.

**Prompt craft:** Match the image to the page's palette and aesthetic direction. Specify the style (3D render, technical illustration, watercolor, isometric, flat vector, etc.) and mention dominant colors from your CSS variables. Use `--aspect-ratio 16:9` for hero banners, `--aspect-ratio 1:1` for inline illustrations. Keep prompts specific — "isometric illustration of a message queue with cyan nodes on dark navy background" beats "a diagram of a queue."

### 3. Style

Apply these principles to every diagram:

**Typography is the diagram.** Pick a distinctive font pairing from Google Fonts. A display/heading font with character, plus a mono font for technical labels. Never use Inter, Roboto, Arial, or system-ui as the primary font. Load via `<link>` in `<head>`. Include a system font fallback in the `font-family` stack for offline resilience.

**Color tells a story.** Use CSS custom properties for the full palette. Define at minimum: `--bg`, `--surface`, `--border`, `--text`, `--text-dim`, and 3-5 accent colors. Each accent should have a full and a dim variant (for backgrounds). Name variables semantically when possible (`--pipeline-step` not `--blue-3`). Support both themes. Put your primary aesthetic in `:root` and the alternate in the media query:

```css
/* Light-first (editorial, paper/ink, blueprint): */
:root { /* light values */ }
@media (prefers-color-scheme: dark) { :root { /* dark values */ } }

/* Dark-first (neon, IDE-inspired, terminal): */
:root { /* dark values */ }
@media (prefers-color-scheme: light) { :root { /* light values */ } }
```

**Surfaces whisper, they don't shout.** Build depth through subtle lightness shifts (2-4% between levels), not dramatic color changes. Borders should be low-opacity rgba (`rgba(255,255,255,0.08)` in dark mode, `rgba(0,0,0,0.08)` in light) — visible when you look, invisible when you don't.

**Backgrounds create atmosphere.** Don't use flat solid colors for the page background. Subtle gradients, faint grid patterns via CSS, or gentle radial glows behind focal areas. The background should feel like a space, not a void.

**Visual weight signals importance.** Not every section deserves equal visual treatment. Executive summaries and key metrics should dominate the viewport on load (larger type, more padding, subtle accent-tinted background zone). Reference sections (file maps, dependency lists, decision logs) should be compact and stay out of the way. Use `<details>/<summary>` for sections that are useful but not primary — the collapsible pattern is in `./references/css-patterns.md`.

**Surface depth creates hierarchy.** Vary card depth to signal what matters. Hero sections get elevated shadows and accent-tinted backgrounds (`node--hero` pattern). Body content stays flat (default `.node`). Code blocks and secondary content feel recessed (`node--recessed`). See the depth tiers in `./references/css-patterns.md`. Don't make everything elevated — when everything pops, nothing does.

**Animation earns its place.** Staggered fade-ins on page load are almost always worth it — they guide the eye through the diagram's hierarchy. Mix animation types by role: `fadeUp` for cards, `fadeScale` for KPIs and badges, `drawIn` for SVG connectors, `countUp` for hero numbers. Hover transitions on interactive-feeling elements make the diagram feel alive. Always respect `prefers-reduced-motion`. CSS transitions and keyframes handle most cases. For orchestrated multi-element sequences, anime.js via CDN is available (see `./references/libraries.md`).

### 4. Deliver

**Output location:** Write to `~/.agent/diagrams/`. Use a descriptive filename based on content: `modem-architecture.html`, `pipeline-flow.html`, `schema-overview.html`. The directory persists across sessions.

**Open in browser:**
- macOS: `open ~/.agent/diagrams/filename.html`
- Linux: `xdg-open ~/.agent/diagrams/filename.html`

**Tell the user** the file path so they can re-open or share it.

## Diagram Types

### Architecture / System Diagrams
Two approaches depending on what matters more:

**Text-heavy overviews** (card content matters more than connections): CSS Grid with explicit row/column placement. Sections as rounded cards with colored borders and monospace labels. Vertical flow arrows between sections. Nested grids for subsystems. The reference template at `./templates/architecture.html` demonstrates this pattern. Use when cards need descriptions, code references, tool lists, or other rich content that Mermaid nodes can't hold.

**Topology-focused diagrams** (connections matter more than card content): **Use Mermaid.** A `graph TD` or `graph LR` with custom `themeVariables` produces proper diagrams with automatic edge routing. Use `look: 'handDrawn'` for informal feel or `look: 'classic'` for clean lines. Use when the point is showing how components connect rather than describing what each component does in detail.

### Flowcharts / Pipelines
**Use Mermaid.** Automatic node positioning and edge routing produces proper diagrams with connecting lines, decision diamonds, and parallel branches — dramatically better than CSS flexbox with arrow characters. Use `graph TD` for top-down or `graph LR` for left-right. Use `look: 'handDrawn'` for sketch aesthetic. Color-code node types with Mermaid's `classDef` or rely on `themeVariables` for automatic styling.

### Sequence Diagrams
**Use Mermaid.** Lifelines, messages, activation boxes, notes, and loops all need automatic layout. Use Mermaid's `sequenceDiagram` syntax. Style actors and messages via CSS overrides on `.actor`, `.messageText`, `.activation` classes.

### Data Flow Diagrams
**Use Mermaid.** Data flow diagrams emphasize connections over boxes — exactly what Mermaid excels at. Use `graph LR` or `graph TD` with edge labels for data descriptions. Thicker, colored edges for primary flows. Source/sink nodes styled differently from transform nodes via Mermaid's `classDef`.

### Schema / ER Diagrams
**Use Mermaid.** Relationship lines between entities need automatic routing. Use Mermaid's `erDiagram` syntax with entity attributes. Style via `themeVariables` and CSS overrides on `.er.entityBox` and `.er.relationshipLine`.

### State Machines / Decision Trees
**Use Mermaid.** Use `stateDiagram-v2` for states with labeled transitions. Supports nested states, forks, joins, and notes. Use `look: 'handDrawn'` for informal state diagrams. Decision trees can use `graph TD` with diamond decision nodes.

**`stateDiagram-v2` label caveat:** Transition labels have a strict parser — colons, parentheses, `<br/>`, HTML entities, and most special characters cause silent parse failures ("Syntax error in text"). If your labels need any of these (e.g., `cancel()`, `curate: true`, multi-line labels), use `flowchart LR` instead with rounded nodes and quoted edge labels (`|"label text"|`). Flowcharts handle all special characters and support `<br/>` for line breaks. Reserve `stateDiagram-v2` for simple single-word or plain-text labels.

### Mind Maps / Hierarchical Breakdowns
**Use Mermaid.** Use `mindmap` syntax for hierarchical branching from a root node. Mermaid handles the radial layout automatically. Style with `themeVariables` to control node colors at each depth level.

### Data Tables / Comparisons / Audits
Use a real `<table>` element — not CSS Grid pretending to be a table. Tables get accessibility, copy-paste behavior, and column alignment for free. The reference template at `./templates/data-table.html` demonstrates all patterns below.

**Use proactively.** Any time you'd render an ASCII box-drawing table in the terminal, generate an HTML table instead. This includes: requirement audits (request vs plan), feature comparisons, status reports, configuration matrices, test result summaries, dependency lists, permission tables, API endpoint inventories — any structured rows and columns.

Layout patterns:
- Sticky `<thead>` so headers stay visible when scrolling long tables
- Alternating row backgrounds via `tr:nth-child(even)` (subtle, 2-3% lightness shift)
- First column optionally sticky for wide tables with horizontal scroll
- Responsive wrapper with `overflow-x: auto` for tables wider than the viewport
- Column width hints via `<colgroup>` or `th` widths — let text-heavy columns breathe
- Row hover highlight for scanability

Status indicators (use styled `<span>` elements, never emoji):
- Match/pass/yes: colored dot or checkmark with green background
- Gap/fail/no: colored dot or cross with red background
- Partial/warning: amber indicator
- Neutral/info: dim text or muted badge

Cell content:
- Wrap long text naturally — don't truncate or force single-line
- Use `<code>` for technical references within cells
- Secondary detail text in `<small>` with dimmed color
- Keep numeric columns right-aligned with `tabular-nums`

### Timeline / Roadmap Views
Vertical or horizontal timeline with a central line (CSS pseudo-element). Phase markers as circles on the line. Content cards branching left/right (alternating) or all to one side. Date labels on the line. Color progression from past (muted) to future (vivid).

### Dashboard / Metrics Overview
Card grid layout. Hero numbers large and prominent. Sparklines via inline SVG `<polyline>`. Progress bars via CSS `linear-gradient` on a div. For real charts (bar, line, pie), use **Chart.js via CDN** (see `./references/libraries.md`). KPI cards with trend indicators (up/down arrows, percentage deltas).

## File Structure

Every diagram is a single self-contained `.html` file. No external assets except CDN links (fonts, optional libraries). Structure:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Descriptive Title</title>
  <link href="https://fonts.googleapis.com/css2?family=...&display=swap" rel="stylesheet">
  <style>
    /* CSS custom properties, theme, layout, components — all inline */
  </style>
</head>
<body>
  <!-- Semantic HTML: sections, headings, lists, tables, inline SVG -->
  <!-- No script needed for static CSS-only diagrams -->
  <!-- Optional: <script> for Mermaid, Chart.js, or anime.js when used -->
</body>
</html>
```

## Quality Checks

Before delivering, verify:
- **The squint test**: Blur your eyes. Can you still perceive hierarchy? Are sections visually distinct?
- **The swap test**: Would replacing your fonts and colors with a generic dark theme make this indistinguishable from a template? If yes, push the aesthetic further.
- **Both themes**: Toggle your OS between light and dark mode. Both should look intentional, not broken.
- **Information completeness**: Does the diagram actually convey what the user asked for? Pretty but incomplete is a failure.
- **No overflow**: Resize the browser to different widths. No content should clip or escape its container. Every grid and flex child needs `min-width: 0`. Side-by-side panels need `overflow-wrap: break-word`. Never use `display: flex` on `<li>` for marker characters — it creates anonymous flex items that can't shrink, causing lines with many inline `<code>` badges to overflow. Use absolute positioning for markers instead. See the Overflow Protection section in `./references/css-patterns.md`.
- **Mermaid interaction**: Every `.mermaid-wrap` container must have a fullscreen toggle button, free scroll-to-zoom (no modifier key), and click-and-drag panning when zoomed. Cursor changes to `grab`/`grabbing`. Max zoom 20x fullscreen, 8x inline. Escape exits fullscreen.
- **Agent task list**: When diagramming an app or multi-screen system (especially Stitch exports), always include a prioritized build task list (P0–P6) with dependencies, domain tags, and screen counts.
- **File opens cleanly**: No console errors, no broken font loads, no layout shifts.
