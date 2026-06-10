# Mona's Project Pulse — Implementation Plan

Date: 2026-06-10T11:48:59Z

Summary
-------
Build a polished, responsive static dashboard (Project Pulse) that reads project data from app/project-data.json and displays project cards with status badges, priorities, progress, and an overall status summary. The app must be minimal and runnable in a Codespace browser preview. The Designer supplies visual hooks and tokens; the Coder implements HTML/CSS/JS and a VS Code launch configuration so a learner can preview easily.

Ordered implementation steps
---------------------------

1. Prepare design tokens & visual hooks (Designer)
   - Deliver a concise design brief: color palette, badge color mapping for statuses, font stack, spacing scale, card elevation, responsive breakpoints, and a small CSS utility list (class names for badge, card, grid).
   - Provide two CSS patterns (compact card and expanded card) and a small example of how the classes are applied to HTML (a tiny HTML snippet).
   - Deliver a small style tokens JSON or CSS variables list the Coder can reference.

2. Create project-data.json sample (Coder)
   - Add app/project-data.json with representative sample data and comments (where useful). Include at least 3 objects: On Track, At Risk, Off Track and differing priorities.
   - Ensure fields: id, name, owner, status, priority, due_date, progress, last_update, description, tags.

3. Scaffold HTML (Coder)
   - Create app/index.html with semantic, accessible markup: header with overall summary area, search/filter input (optional), container for project card grid, and a small "no data" state.
   - Link to app/styles.css and app/main.js (or inline small script).
   - Add a <noscript> note and an inline fallback script block (see "CORS/file protocol fallback" in step 5).

4. Implement core CSS (Coder, using Designer tokens)
   - Create app/styles.css implementing the Designer tokens and patterns.
   - Styles for: responsive grid, project card, badges, priority pill, progress bar, header summary strip, hover states, accessible focus outlines, and print-friendly settings.
   - Include CSS variables at top for colors and spacing.

5. Implement JS to load and render data (Coder)
   - Create app/main.js (or inline small JS in index.html) to fetch app/project-data.json, parse it, and render project cards into the grid.
   - Implement fallback logic: if fetch fails (due to file:// restrictions), read an inline JSON <script id="project-data" type="application/json"> as fallback.
   - Implement robust handling of missing fields (show placeholders) and limit long text with ellipses and "show more" accessible toggles.
   - Add simple client-side sorting: by status priority (Off Track → At Risk → On Track) and progress descending. (Optional but recommended.)
   - Update the header summary with counts per status and an overall progress average.

6. Add VS Code launch configuration (Coder)
   - Create .vscode/launch.json with two configurations:
     a) "Start static server + Open in Browser" — runs a lightweight http server (using npx http-server or a node static server) then launches the URL in pwa-chrome/pwa-msedge.
     b) "Open index.html (file://) with fallback" — launches index.html directly for quick preview (note: fetch may fail; fallback JSON covers this).
   - Add clear comments/instructions inside launch.json for the Orchestrator.

7. Accessibility & Responsiveness polish (Designer + Coder)
   - Designer reviews contrast & interactions; Coder tweaks CSS to meet suggestions.
   - Ensure keyboard navigation and aria attributes for badges, progress bars, and "show more".

8. Validation & automated smoke checks (Coder)
   - Add a small validation script in docs/validate-project-pulse.md (instructions) and optionally a tiny Node script (optional) that fetches and parses app/project-data.json and checks expected keys and number of items.
   - Provide manual test steps and suggested test data scenarios.

9. Final review & integration (Orchestrator)
   - Run the integration/verification checklist, mark tasks done, open PR.

File assignments (owner and exact changes/content)
-------------------------------------------------

Note: All file paths are relative to repository root. Owners: Designer or Coder.

- app/index.html — Owner: Coder
  - Create a complete HTML file with:
    - <head> linking to app/styles.css and a small script tag for app/main.js (or inline).
    - Header area with title "Project Pulse", a top summary strip (counts and avg progress) and an optional search/filter input.
    - <main> containing a container element with id="project-grid" where cards are injected.
    - A fallback inline JSON block:
      <script id="project-data" type="application/json"> ... </script>
      This block contains the exact same sample projects as app/project-data.json and is used only if fetch fails.
    - <noscript> message and accessible aria landmarks.
  - Validation expectations: index.html must render with JS enabled and without JS fallback should show an instruction.

- app/styles.css — Owner: Coder (implementing Designer tokens/patterns)
  - Create CSS with:
    - :root CSS variables for colors, status badge colors, spacing scale, font stack.
    - Global resets + utility classes (u-hidden, u-ellipsis, sr-only).
    - Header & summary strip styles, responsive grid (CSS grid), card styles with shadows, badge styles, progress bar styling, priority pill styles, media queries for mobile/tablet/desktop.
    - Focus states and accessible color contrast adjustments.
  - Designer will supply tokens and preferred values; Coder implements them in this file.
  - Validation expectations: CSS must display cards in a single column on narrow screens, multi-column on wide screens, badge colors match Designer tokens.

- app/project-data.json — Owner: Coder
  - Create JSON file with at least three sample projects. Example content (exact sample required in plan below):

    [
      {
        "id": "proj-001",
        "name": "Website Redesign",
        "owner": "Aisha Khan",
        "status": "On Track",
        "priority": "Medium",
        "due_date": "2026-09-30",
        "progress": 72,
        "last_update": "2026-06-01",
        "description": "Full homepage and product pages redesign to improve conversion.",
        "tags": ["UX", "Frontend"]
      },
      {
        "id": "proj-002",
        "name": "Payments Integration",
        "owner": "Carlos Mendez",
        "status": "At Risk",
        "priority": "High",
        "due_date": "2026-07-15",
        "progress": 45,
        "last_update": "2026-06-08",
        "description": "Replace legacy gateway; fixes needed for PCI compliance.",
        "tags": ["Backend", "Security"]
      },
      {
        "id": "proj-003",
        "name": "Mobile App Launch",
        "owner": "Priya Singh",
        "status": "Off Track",
        "priority": "High",
        "due_date": "2026-06-25",
        "progress": 20,
        "last_update": "2026-06-09",
        "description": "iOS and Android release — need QA and app store approvals.",
        "tags": ["Mobile", "QA"]
      }
    ]

  - Validation expectations: JSON must be valid (linted), and contain these fields on each object.

- app/main.js (or inline script inside index.html) — Owner: Coder
  - Implement logic to:
    - Fetch app/project-data.json via fetch('./project-data.json').
    - If fetch fails or response not ok, parse the inline <script id="project-data"> fallback to get same data.
    - Render header summary (counts and avg progress).
    - Render project card markup for each project.
    - Attach event handlers for "show more" long descriptions and optional sort/filter.
    - Robustly handle missing fields: show "—" for missing owner, "Unknown" status mapping to neutral badge, progress default 0.
    - Add console debug log statements for quick debugging.
  - Validation expectations: main.js should populate DOM with number of cards equal to project-data.json length.

- .vscode/launch.json — Owner: Coder
  - Create a launch.json with two configurations and comments:
    1. "Launch Static Server & Open in Chrome" — launches a small node process: "npx http-server ./app -p 3000 -c-1" (or "npx serve app -l 3000") then opens http://localhost:3000 in the browser debug adapter (pwa-chrome).
    2. "Open index.html (file://)" — opens file:///.../app/index.html in debug adapter for quick preview. (Note about fetch and fallback.)
  - Include preLaunchTask comments and instructions "If you don't have http-server, run: npm i -g http-server" as guidance.
  - Validation expectations: Using the first configuration should open http://localhost:3000 and app loads via HTTP (no CORS issues).

- docs/validate-project-pulse.md — Owner: Coder
  - Add a simple validation document listing manual and automated checks (see Validation Expectations below). Include sample curl/Node commands to verify JSON parses and counts.

Dependencies (which steps depend on others)
-----------------------------------------

- Step 1 (Design tokens) must complete before Step 4 (Implement core CSS) and before the Coder finalizes visual styles in app/styles.css.
- Step 2 (project-data.json) can be done in parallel with Step 1; Coder can create sample data even before design tokens are finalized.
- Step 3 (Scaffold HTML) depends on Step 1 (for class names) but can start with placeholder classes; minimal dependency.
- Step 4 (CSS) depends on Step 1 (tokens) and on Step 3 (class names used in HTML).
- Step 5 (JS) depends on Step 2 (data file) and Step 3 (HTML container elements) to know where to inject markup.
- Step 6 (launch.json) depends on Step 5 to ensure the server opens the correct path but can be prepared earlier.
- Step 7 (Accessibility polish) depends on Steps 1, 4, 5.
- Step 8 (Validation Scripts/docs) depends on Steps 2, 3, 4, 5.
- Step 9 (Final review) depends on all previous steps.

Parallel work decisions (which files/steps can be done in parallel and why)
-------------------------------------------------------------------------

Work that can run in parallel:
- Step 1 (Designer tokens) and Step 2 (project-data.json) — data creation does not depend on final tokens.
- Step 3 (Scaffold HTML) and Step 1 — Coder can scaffold HTML with placeholder class names; Designer will confirm or adjust class names.
- Step 4 (CSS implementation) can be prepared in parallel to Step 5 (JS) once Step 1 is delivered; CSS and JS touch different files and will not conflict if the HTML scaffold is stable.
- Step 6 (launch.json) and Step 2 (data JSON) — launch config does not depend on CSS/JS content.
Parallelization rationale:
- Minimizes idle time: Coder can scaffold and populate data while Designer finalizes aesthetics.
- CSS and JS operate on different concerns (presentation vs behavior) and can be worked on concurrently with agreed class names.

Work that must run sequentially
------------------------------
- CSS finalization (Step 4) should finalize after Designer tokens (Step 1).
- JS rendering (Step 5) must run after the final HTML scaffold (Step 3) and after the data file (Step 2) is available.
- Accessibility polish (Step 7) must occur after the UI is implemented.

Designer responsibilities
-------------------------
- Deliver design tokens: color variables, spacing scale, type scale, and badge color mapping.
- Provide two card patterns and usage examples (compact and expanded).
- Provide guidance on accessible color contrasts and example focus states.
- Review final designs and provide any necessary tweaks to CSS for visual polish.
- Provide example images or icons if desired (optional).

Coder responsibilities
---------------------
- Create files listed above and implement the app functionality.
- Implement robust fetch + fallback to support Codespace file:// and HTTP preview.
- Ensure accessible semantic markup and keyboard interactions.
- Write helpful comments in .vscode/launch.json to help a learner preview the app.
- Create project-data.json sample and keep it consistent with inline fallback in index.html.

Validation expectations
-----------------------

Manual validation (per step)
1. Designer tokens
   - Check tokens: colors, spacing, type scale present in a small tokens document.
   - Ensure badge color mapping includes "On Track", "At Risk", "Off Track" and fallback color.

2. app/project-data.json
   - Use a JSON linter or run node -e "console.log(JSON.parse(require('fs').readFileSync('app/project-data.json','utf8')).length)"
   - Expected: 3 entries (per sample).
   - Ensure no trailing commas and valid ISO date strings.

3. app/index.html + app/main.js
   - Open app using the preferred launch configuration:
     - Preferred: Run "Launch Static Server & Open in Chrome" in .vscode/launch.json.
     - Alternative: open file:///.../app/index.html; verify fallback JSON renders if fetch is blocked.
   - Manual checks:
     - Header shows title "Project Pulse".
     - Summary strip shows three status counts and an average progress (calculated).
     - There are 3 project cards rendered and each card includes: title, owner, status badge, priority pill, progress bar, due date, short description.
     - Badges use the correct colors from the Designer token mapping.
     - Cards are keyboard-navigable (Tab focusing a card shows visible focus outline).
     - Resize browser window: on narrow widths cards stack in one column, on wider widths grid shows multiple columns.
     - Click "show more" (if description truncated) expands to show full description and is keyboard accessible.
     - Simulate missing fields by temporarily editing project-data.json (remove owner or progress) and verify placeholders ("—" or 0%) show.

4. app/styles.css
   - Visual checks:
     - Contrast meets a11y thresholds (use browser devtools color contrast analyzer extension).
     - Hover/active states visually distinct.
     - Priority pills (High/Medium/Low) are visually distinct.

5. .vscode/launch.json
   - Launch "Start Static Server & Open in Chrome" and confirm the app opens at http://localhost:3000 and functions (fetch should succeed).
   - Launch "Open index.html (file://)" and confirm the app still displays by using the inline fallback JSON if fetch fails.

Automated validation (recommended)
- Node smoke test (optional script or run in Codespace terminal):
  - Run: node -e "const fs=require('fs');const data=JSON.parse(fs.readFileSync('app/project-data.json','utf8')); if(!Array.isArray(data)) throw Error('not array'); console.log('OK',data.length+' projects');"
  - Expected output: OK 3 projects
- DOM smoke test (optional, create quickly with Playwright or Puppeteer later):
  - Start the static server and run a script that loads http://localhost:3000, counts document.querySelectorAll('.project-card').length and asserts it equals JSON length.
- Linting:
  - CSS/HTML lint rule checklist (colors, alt attributes for images if any).

Edge cases and risks
--------------------
- CORS / file:// restrictions: fetching app/project-data.json may fail when opening app/index.html via file://. Mitigation: provide .vscode/launch.json that launches a simple HTTP server; implement inline JSON fallback in index.html.
- Large data volumes: many projects may make the page heavy. Mitigation: implement reasonable card rendering patterns, lazy rendering (if many items) or virtualized list in future. For now, handle up to ~200 cards with CSS grid; note performance risk beyond that.
- Missing fields & malformed JSON: If project objects miss keys, render sensible placeholders. If JSON is invalid, show a friendly UI error message and log the error in console.
- Long text overflow: long project names or descriptions could break layout. Mitigation: apply u-ellipsis to one-line titles and truncated descriptions with accessible "show more".
- Accessibility issues: color contrast and keyboard focus must be explicitly checked. Designer must provide accessible token choices.
- Browser compatibility: CSS Grid and modern features broadly supported but test in Chromium-based Codespace preview. If older browsers required, degrade gracefully.
- Integration conflicts: Designer and Coder may choose different class names. Mitigation: agree on class names in Step 1 deliverable and keep a short class name map document.

Open questions for the Orchestrator
----------------------------------
1. Do you prefer the JSON to be strictly external (project-data.json only) or accept the inline fallback in index.html for local preview?
2. Which static server would you prefer the launch.json to use by default (npx http-server vs npx serve vs a tiny Node script)? Is installing a global http-server acceptable for learners?
3. Do you want search and filters included in the initial iteration, or should they be deferred to a future enhancement?
4. Are there any corporate brand colors, typefaces, or logos that must appear on the dashboard (Designer should know)?
5. Is there a preference for how statuses map to colors (e.g., Off Track = red, At Risk = amber, On Track = green) or want a different palette (Designer should confirm)?
6. Should the app include sample icons (owner avatars, tags) or keep everything text-only initially?
7. Is there an expectation to support right-to-left languages in this iteration?

Final integration / verification checklist (for Orchestrator)
--------------------------------------------------------------
- [ ] Designer delivered tokens + card usage examples in a short doc or comment.
- [ ] app/project-data.json exists and validates as JSON; contains at least 3 sample projects (see sample below).
- [ ] app/index.html created and includes header, summary, project-grid container, inline fallback JSON, and links to styles and JS.
- [ ] app/styles.css created and uses CSS variables for tokens; responsive grid and card styles implemented.
- [ ] app/main.js (or inline script) fetches project-data.json, falls back to inline JSON, updates header summary, and renders project cards.
- [ ] .vscode/launch.json created with "Launch Static Server & Open in Chrome" and "Open index.html (file://)" configurations and includes instructions.
- [ ] Manual smoke test: open via launch.json static server → summary and 3 cards render.
- [ ] Manual fallback test: open file://index.html and confirm inline fallback renders (if fetch blocked).
- [ ] Accessibility checks: tab navigation works, focus states visible, contrast acceptable.
- [ ] Responsive checks: cards stack on small screens and form grid on larger screens.
- [ ] Edge cases: test with a modified project-data.json removing fields and confirm placeholders appear.
- [ ] Final review & PR opened with description of how to preview and what to look for.

Small example entries for app/project-data.json (required sample)
----------------------------------------------------------------
Use these three sample objects exactly as shown (put into app/project-data.json):

[
  {
    "id": "proj-001",
    "name": "Website Redesign",
    "owner": "Aisha Khan",
    "status": "On Track",
    "priority": "Medium",
    "due_date": "2026-09-30",
    "progress": 72,
    "last_update": "2026-06-01",
    "description": "Full homepage and product pages redesign to improve conversion.",
    "tags": ["UX", "Frontend"]
  },
  {
    "id": "proj-002",
    "name": "Payments Integration",
    "owner": "Carlos Mendez",
    "status": "At Risk",
    "priority": "High",
    "due_date": "2026-07-15",
    "progress": 45,
    "last_update": "2026-06-08",
    "description": "Replace legacy gateway; fixes needed for PCI compliance.",
    "tags": ["Backend", "Security"]
  },
  {
    "id": "proj-003",
    "name": "Mobile App Launch",
    "owner": "Priya Singh",
    "status": "Off Track",
    "priority": "High",
    "due_date": "2026-06-25",
    "progress": 20,
    "last_update": "2026-06-09",
    "description": "iOS and Android release — need QA and app store approvals.",
    "tags": ["Mobile", "QA"]
  }
]

Notes and pragmatic recommendations
-----------------------------------
- Fetch + inline fallback approach ensures the learner can open the file via file:// if they choose, but the preferred preview in Codespace should use the static server launch configuration to avoid fetch restrictions.
- Keep JS minimal & dependency-free so the app remains simple to inspect and edit (vanilla JS recommended).

If anything above is unclear or you want me to split these steps into Git-friendly tasks (with file-level patch content suggestions), say which task granularity you prefer and I will produce the next artifact.
