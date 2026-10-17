# SWARMFORGE — v1 Rebrand Change Report

**Branch:** `feat/swarmforge-rebrand` · **Commit:** `c71146b` (baseline: `5f5f230`, upstream `5625efbb555bb657f0461c7b7780c9a1eb25e8b9`)
**Scope:** frontend-first rebrand per the SWARMFORGE PRD — user-facing naming, branding, dashboard polish, metadata, attribution.

---

## 1. Files changed (20 files, +123 / −70)

| Area | Files |
| --- | --- |
| Dashboard tokens | `web/static/css/tokens.css` (ember accent palette, warmed user bubbles) |
| Dashboard shell | `web/static/css/shell.css` (`.brand-mark`, `.brand-wordmark`) |
| Dashboard pages | `web/static/app.html`, `chat.html`, `orchestration.html`, `threads.html`, `usage.html`, `warehouse.html` (titles, brand markup, Korean web-token labels) |
| Server-rendered pages | `src/support/dashboardHtml.ts` (supervisor title, brand, chat prefix), `src/issues/issueBoardHtml.ts` (issues title, header logo) |
| Metadata | `package.json`, `package-lock.json` (name → `swarmforge`; bin unchanged: `openswarm`), `src/support/updateNotifier.ts` (+ 3 expected-string updates in its two test files) |
| Test literals | `src/cli/checkHandler.test.ts` (registry project id → `swarmforge`, ×2), `src/support/healthEndpoint.test.ts` (/app shell brand assertion → `SWARMFORGE`) |
| Docs / attribution | `README.md` (rebranded header, fork notice, identity pass), `NOTICE.md` (new), `assets/swarmforge_mark.svg` (new) |

Not committed (kept as workspace evidence): `screenshots/before/`, `screenshots/after/`, one throwaway patched check script (see §5).

## 2. What changed visually

- **Accent color:** indigo/blue → **ember orange** (`--accent: #f0883e`, hover `#ffa657`, tint `rgba(240,136,62,0.14)` in dark; `#bc4c00` / `#953800` / `rgba(188,76,0,0.1)` in light). All semantic colors (status green/red/amber, chart series, role colors, diff colors) intentionally unchanged.
- **Brand mark:** inline `currentColor` hexagon+triangle SVG in every page's topbar, paired with a bold `SWARMFORGE` wordmark; standalone asset `assets/swarmforge_mark.svg`.
- **Names:** page titles (`SWARMFORGE :: Supervisor`, `SWARMFORGE — Cockpit`, …), README header, supervisor chat placeholder ("Message SWARMFORGE…"), warehouse/usage token labels ("SWARMFORGE 웹 토큰").
- **User bubbles** warmed to `#251a12` / `#ffe9d6` to sit with the ember accent; agent/operator role colors unchanged.

## 3. Test results

All commands run on Windows with Node v24.20.0 on this working copy.

| Gate | Command | Result |
| --- | --- | --- |
| Lint | `npm run lint` | **0 errors**, 10 pre-existing warnings (all in `src/ops/velaAutodeploy.script.test.ts`, untouched by this change) |
| Type check | `npm run typecheck` | **clean** |
| Build | `npm run build` | **success** (tsc + `dist/web-static` refresh verified: 48/48 files match `web/static`) |
| Targeted tests | vitest: `tests/web/**`, `src/support/dashboardHtml.test.ts`, `src/support/updateNotifier*.test.ts` | **23 files / 373 tests — all pass** (covers every changed surface) |
| Test-literal alignment | vitest: `src/cli/checkHandler.test.ts` + `src/support/healthEndpoint.test.ts` | **2 files / 76 tests — all pass** (after aligning 3 brand-literal expectations, incl. commit `c71146b`) |
| Full suite | `npm test` | **6941 passed**, 24 skipped, **360 failed (7325)** on Windows — every failure classified as environmental, pre-existing, flake, or already-fixed; see §5 baseline comparison |

### Responsive regression gate (rebranded dashboard, live server)

`scripts/check-dashboard-responsive.ts` equivalent run (see §5 for the two-line Windows note):

```
PASS 60 responsive page checks
```
6 pages (supervisor, app, chat, orchestration, threads, warehouse) × 5 widths (320/390/768/1024/1440) × light+dark.
Asserts: no page overflow at any width, no mobile viewport expansion, desktop panels within viewport, **zero JavaScript errors**, navigation reachable/not obscured (incl. horizontal-scroll nav), sidebar drawer below topbar at ≤900px, composer not clipped in short viewports, all input font-sizes ≥16px at ≤900px, threads form/detail non-overlap. Machine-readable log: `screenshots/after/results.json`.

### Smoke test (live dashboard, `node dist/cli.js dash --no-open --port 3847`)

| Check | Result |
| --- | --- |
| `GET /` `…/app` `…/chat` `…/orchestration` `…/threads` `…/warehouse` `…/usage` `…/issues` | all **200**, all pages present `SWARMFORGE` branding and correct titles |
| `GET /api/health` | 200 (`app:"openswarm"` — internal identity, deliberately unchanged) |
| `GET /api/stats`, `/api/tasks`, `/api/pipeline` | 200, friendly empty state (`runningTasks:0`, `running:[] queued:[]`, `stages:[]`) |
| Empty-state text on pages | "no repositories", "No pipeline events", "Deployed agents will report progress here." — no fabricated activity |
| JS console | no page errors across all 60 gate checks |

### Before / after screenshots

`screenshots/before/` (pristine upstream `5f5f230` served from a worktree) and `screenshots/after/` (rebuilt dashboard) each contain 24 images:
6 pages × {1440×900, 390×844} × {light, dark}. Verified pairs: cockpit/supervisor (chat prefix + accent + brand), app (sidebar selection, active-work empty state), chat (role colors semantic, warmed operator bubbles), orchestration (legend role colors kept), threads (light-theme accent), warehouse (provisioned sample tree).

## 4. Deliberately unchanged (v1 deferrals, per PRD scope)

- **CLI command `openswarm`** and all `OPENSWARM_*` env vars / `X-OpenSwarm-Token` header / actor ids / localStorage keys — internal contracts that would break existing workflows and tests.
- **TUI / ASCII banner** (`src/support/banner.ts`), **desktop Tauri app** (`desktop/`), **GitHub Action** (`action.yml`), and all other backend sources (≈847 "OpenSwarm" occurrences in `src/`) — deferred beyond v1.
- **LICENSE** text and upstream URLs in docs (pointing to real upstream resources); README keeps `openswarm …` install/usage commands.
- **`/api/health` `app:"openswarm"`** response field — machine-consumed identity seam (test at `healthEndpoint.test.ts:96` updated to keep pinning it to `openswarm`).

## 5. Environment notes & known test failures

- **Windows baseline failures:** the full suite fails the same way on the **pristine upstream checkout** — rerunning the exact 115 failed test files on a `5f5f230` worktree (same machine) reproduced **107/115 files failing identically (346 tests), with zero files failing only on baseline**. The failures are environment/platform issues, not rebrand regressions — dominant causes: CRLF-vs-LF file contents (`\r\n` vs `\n`), POSIX-only `find`, symlink creation `EPERM` (no Windows developer mode), missing `gh` CLI, backslash-vs-slash path expectations, `chmod` semantics.
  - **Baseline comparison (the remaining 8 files):** 3 were real brand-literal test pins — `checkHandler.test.ts` expected registry project id `'openswarm'` (derived from `package.json` name, now `swarmforge`) ×2, and `healthEndpoint.test.ts` asserted the served `/app` shell contains `OpenSwarm` ×1. All aligned to the fork identity in commit `c71146b`; both files now pass 76/76. The other 5 (`publicationFence`, `rateLimitError`, `shellCommandGuard`, `velaAutodeploy.script`, `worktreeManager.concurrentCreate`) plus a 30s timeout in `pairPipeline.cancelIsolation` are load-sensitive flakes: each passed on isolated reruns of the rebranded tree. **Net rebrand regressions: 0.**
- **`scripts/check-dashboard-responsive.ts` is POSIX-only as shipped:** its static-asset guard `file.startsWith(staticRoot + '/')` always throws on Windows (`path.resolve` yields `\`). The gate above was run via a copy with exactly two lines changed (import `sep`; separator-normalized assertion) — all 60 checks and assertions otherwise identical. Copy: `scripts/tmp-run-check-responsive.ts` (untracked).
- **Warehouse page** needs `OPENSWARM_WAREHOUSE_ROOT` pointing at a real directory; without it `/api/warehouse/tree` returns `503 Warehouse is not provisioned` (product behavior, unrelated to branding). A sample warehouse was provisioned for the screenshot/gate runs.
- **npm publishing pending:** the package is not yet published as `swarmforge`; the auto-updater now checks the `swarmforge` registry entry and silently no-ops (404) until it exists — by design, so it can never reinstall upstream over the fork.

## 6. Reproduce

```powershell
npm install
npm run lint; npm run typecheck; npm run build
npm test
node dist/cli.js dash --no-open --port 3847          # dashboard
npx tsx scripts/check-dashboard-responsive.ts http://localhost:3847 out   # Linux/macOS
```

Before-shots: `git worktree add <dir> 5f5f230` and run the dashboard from that tree (no build: `npx tsx src/cli.ts dash --no-open --port 3848`).
