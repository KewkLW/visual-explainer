---
description: Generate a visual test suite + Playwright test files from a Stitch export analysis
skill: stitch-visual-explainer
---
Generate tests for the Stitch export at: $@

Follow the stitch-visual-explainer skill's "Test Generation" workflow:

1. **Scan & Classify** — Reuse the Stitch Export Analysis scanning workflow. List all screen subdirectories, read `code.html` from key screens, extract navigation patterns, UI components, auth methods, and design tokens. Group screens into domains.

2. **Derive Tests** — Apply the test derivation algorithm from the skill:
   - `NAV_xxx` from every navigation flow edge (bottom nav, links, cards)
   - `AUTH_xxx` from auth flow paths (login, signup, reset)
   - `CMP_xxx` from every domain screen (verify key elements exist)
   - `INT_xxx` from interactive elements (toggles, selectors, pickers)
   - `FRM_xxx` from forms (empty/valid/invalid submission)
   - `A11Y_xxx` from screen structure (heading hierarchy, labels, ARIA)
   - `VIS_xxx` from `screen.png` files (screenshot baselines)
   - `E2E_xxx` from P0–P1 agent tasks (full user journeys)

3. **Generate Visual Suite** — Produce an HTML page using the test-suite template aesthetic (electric indigo/lime palette, Sora + IBM Plex Mono fonts). Include all 7+ sections: summary KPIs with coverage ring, coverage heatmap, dependency graph, domain test sections, file map table, and run instructions. Read `./templates/test-suite.html` and `./references/css-patterns.md` for patterns before generating.

4. **Generate Playwright Files** — Write runnable test files to `~/.agent/tests/<app-name>/` using the Page Object Model structure defined in the skill. Include `playwright.config.ts`, `fixtures.ts`, spec files per domain, page objects, and copy `screen.png` baselines.

5. **Deliver** — Write the HTML test suite to `~/.agent/diagrams/<app-name>-tests.html` and open in browser. Report the Playwright test file locations and total test count.

Run the quality check: every screen has a CMP test, every flow edge has a NAV test, every P0–P1 task has an E2E test.
