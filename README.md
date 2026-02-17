# stitch-visual-explainer

**A [Stitch](https://stitch.withgoogle.com/) companion skill that turns exported screen designs into interactive flow diagrams, architecture maps, and prioritized agent task lists.**

> **Based on [visual-explainer](https://github.com/nicobailon/visual-explainer) by [@nicobailon](https://github.com/nicobailon).** This fork extends the original with Stitch-specific screen analysis, agent-ready task generation, and fullscreen diagram interaction. All original features, templates, and design principles are preserved. MIT licensed — same as upstream.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](LICENSE)
[![Based on](https://img.shields.io/badge/Based%20on-visual--explainer-blue?style=for-the-badge)](https://github.com/nicobailon/visual-explainer)

## What This Fork Adds

You export screens from Stitch. Each screen becomes a folder with `code.html` (Tailwind prototype) and `screen.png` (screenshot). This skill reads those exports and generates:

1. **Visual flow diagrams** — Mermaid-based navigation maps showing how every screen connects, color-coded by domain
2. **Domain breakdown** — Cards grouping screens into logical clusters (auth, home, workout, nutrition, etc.)
3. **Prioritized agent task list** — A P0–P6 build roadmap with dependencies, so AI agents know exactly what to build and in what order
4. **Tech stack analysis** — Extracts fonts, colors, frameworks, and design patterns from the HTML
5. **User journey maps** — Core interaction loops visualized as flowcharts
6. **Test suite generation** — A visual HTML test matrix + runnable Playwright test files, derived from navigation flows, domain screens, and agent task acceptance criteria

The output is a single self-contained HTML file with interactive diagrams (fullscreen toggle, scroll-to-zoom, drag-to-pan), responsive sidebar TOC, and both light/dark theme support.

### Agent Task Lists

The key addition for agent workflows. Every app flow diagram includes a **prioritized build task list** designed for AI agents to consume:

| Priority | Meaning | Example |
|----------|---------|---------|
| **P0** | Foundation — nothing works without this | Design system, app shell, auth |
| **P1** | Core loop — the main reason the app exists | Workout logging, primary CRUD |
| **P2** | Retention features — what keeps users coming back | Programs, nutrition tracking |
| **P3** | Depth features — enriching existing domains | Custom foods, barcode scanner, recipes |
| **P4** | Polish — analytics, settings, data export | Trends, recovery metrics, preferences |
| **P5** | Nice-to-haves — wellness, monetization | Meditation, upgrade flows, ads |
| **P6** | Admin & stubs — ship last | Debug panels, coming-soon placeholders |

Each task includes: domain tag, screen count, dependency chain, and a description detailed enough for an agent to start coding from.

### Interaction Changes

- **Fullscreen toggle** replaces +/- zoom buttons — click to expand any diagram to fill the viewport
- **Free scroll-to-zoom** — mouse wheel zooms directly, no Ctrl/Cmd modifier needed
- **Drag-to-pan** when zoomed in, Escape to exit fullscreen
- Zoom limits: 20x in fullscreen, 8x inline

## Install

```bash
# Claude Code
git clone https://github.com/KewkLW/visual-explainer.git ~/.claude/skills/visual-explainer

# Other agents — point at SKILL.md or paste its contents into your system prompt
```

## Usage

### Analyzing a Stitch Export

Export your screens from Stitch, then point the agent at the folder:

```
> use visual-explainer to explain the flow of C:\path\to\stitch_export
> /generate-web-diagram for the app at ~/Downloads/my_stitch_project
> /stitch-analyze ~/exports/fitness_app
```

The agent will:
1. Scan all screen directories for `code.html` files
2. Extract titles, navigation patterns, UI elements, and design tokens
3. Group screens into domains based on naming and content
4. Generate a multi-section HTML page with flow diagrams, domain cards, tech stack, and build tasks
5. Open it in the browser

### All Commands

| Command | What it does |
|---------|-------------|
| `/generate-web-diagram` | Generate an HTML diagram for any topic |
| `/stitch-analyze` | Analyze a Stitch export folder — flow + domains + agent tasks |
| `/diff-review` | Visual diff review with architecture comparison |
| `/plan-review` | Compare a plan against the codebase |
| `/project-recap` | Architecture snapshot for context-switching |
| `/fact-check` | Verify claims in documents against code |
| `/stitch-generate-tests` | Generate test suite (visual HTML + Playwright files) from a Stitch export |

## How It Works

```
Stitch Export (folders with code.html + screen.png)
    ↓
SKILL.md (workflow + Stitch analysis rules + design principles)
    ↓
references/           ← agent reads before each generation
├── css-patterns.md   (layouts, animations, theming, depth tiers)
├── libraries.md      (Mermaid theming, Chart.js, anime.js, fonts)
└── responsive-nav.md (sticky sidebar TOC)
    ↓
templates/            ← agent reads the matching reference template
├── architecture.html (CSS Grid cards)
├── mermaid-flowchart.html (Mermaid + ELK)
├── data-table.html   (tables with KPIs)
└── test-suite.html   (test matrix + coverage heatmap)
    ↓
~/.agent/diagrams/app-name-flow.html  → opens in browser
~/.agent/tests/app-name/              → Playwright test files
```

## Upstream

This is a fork of **[nicobailon/visual-explainer](https://github.com/nicobailon/visual-explainer)** — an agent skill that turns complex terminal output into styled HTML pages. All credit for the core architecture, design system, template structure, reference patterns, and prompt templates goes to [@nicobailon](https://github.com/nicobailon). This fork adds Stitch-specific analysis and agent task generation on top of that foundation.

## What's Preserved from Upstream

Everything. All original features work exactly as before:
- 11 diagram types (architecture, flowchart, sequence, ER, state machine, mind map, data table, timeline, dashboard, etc.)
- Self-contained HTML with dual light/dark themes
- Interactive Mermaid diagrams with ELK layout
- Proactive table rendering (4+ rows → HTML instead of ASCII)
- Reference templates with distinct palettes for variety
- AI-generated illustrations via surf-cli (optional)
- Six prompt templates (generate, diff-review, plan-review, project-recap, fact-check, stitch-analyze)

## Limitations

- Requires a browser to view output
- Stitch exports must follow the standard folder structure (`screen_name/code.html` + `screen_name/screen.png`)
- Agent task prioritization is heuristic — review and adjust for your specific project
- Switching OS theme requires page refresh for Mermaid SVGs

## Credits

- **[@nicobailon](https://github.com/nicobailon)** — Original [visual-explainer](https://github.com/nicobailon/visual-explainer) skill. Core architecture, design system, templates, and all reference patterns.
- Borrows ideas from [Anthropic's frontend-design skill](https://github.com/anthropics/skills) and [interface-design](https://github.com/Dammyjay93/interface-design).

## License

MIT — same as upstream.
