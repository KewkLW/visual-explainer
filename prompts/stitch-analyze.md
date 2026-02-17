---
description: Analyze a Stitch export folder — generate flow diagrams, domain breakdown, and prioritized agent task list
skill: stitch-visual-explainer
---
Analyze the Stitch export at: $@

Follow the stitch-visual-explainer skill's "Stitch Export Analysis" workflow:

1. **Scan** — List all screen subdirectories. Read `code.html` from key screens to extract: page titles, navigation structure, UI components, design tokens (Tailwind config, colors, fonts), auth methods.

2. **Classify** — Group screens into domains (auth, home, core features, settings, monetization, admin, etc.) and assign each a distinct color.

3. **Match aesthetic** — Extract the app's design language from its CSS/Tailwind config and use that same palette, fonts, and visual style for the output page.

4. **Generate** — Produce a multi-section HTML page with:
   - KPI overview (screen count, domains, nav tabs)
   - Auth flow diagram (Mermaid)
   - Navigation structure (bottom nav / tab bar)
   - Full system flow (all screens, color-coded by domain, with subgraphs)
   - Domain breakdown (card grid with screen listings)
   - Tech stack table (extracted from the HTML)
   - Primary user journey (core interaction loop)
   - **Prioritized agent task list** (P0–P6 with dependencies, domain tags, screen counts, and descriptions detailed enough for agents to implement from)

5. **Deliver** — Write to `~/.agent/diagrams/` with a descriptive filename and open in browser.

Read the reference templates and CSS patterns before generating. Use Mermaid with fullscreen toggle and scroll-to-zoom for all diagrams.
