# Headless Browser Learnings

## [2026-07-26] Lesson: A self-driving harness page plus `--screenshot` eyeballs a webview change without a test-framework dependency
**ID**: b3d1c9a2
**Category**: tool
**Context**: When a change is visual (renderer, layout, encoding) and needs to be *seen* before it is called done, but the project has deliberately no Playwright/Puppeteer dependency — e.g. after retiring a screenshot-diff suite as a standing CI gate.
**Learning**: Installed Chrome and Edge both accept `--headless=new --screenshot=<abs path> --window-size=W,H --virtual-time-budget=<ms> --user-data-dir=<tmp> <url>` and will render a local dev page to PNG with no npm dependency at all. Two details make it work on a graph webview: (1) `--virtual-time-budget` must exceed the page's own animation/layout settle time, or the capture lands mid-layout; (2) navigation *inside* the app can't be driven from the CLI, so put the driving in a throwaway copy of the harness page — read a query param, call the app's existing test hook (`window.__ATLAS_RENDERER__.setScope(...)`), `await` a fixed settle delay, then capture. If the harness server already has a POST-to-save endpoint, the page can also save `cy.png()` itself, giving a canvas-only image alongside the browser's full-page one. Write the throwaway page under `temp*/` so it is obviously not a fixture. `--screenshot` fails with "Access is denied" if a previous browser instance still holds the target file — use distinct filenames per run.
**Evidence**: CodeAtlas C++ plan-01 Part C step C1 (relationship-first Components scene). Unit tests and a fixture-count script both passed, but only the screenshot showed the three things that actually mattered: kind-group bins were gone, edges rendered by default, and a class-nested type drew inside its owner's compound. The same image also surfaced the still-unfixed label-duplication defect (`evaluateHand(const Hand&): int evaluateHand`) and the unmeaningful layout — both known, both scheduled later in the same plan, neither visible from test output.
**Confidence**: medium
**Validations**: 1
**Projects**: codeatlas-cpp
