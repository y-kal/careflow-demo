# Careflow

A standalone, fictional 74-bed quarantine-centre operations prototype. Vanilla HTML, CSS and JavaScript; no build step, dependencies, API keys, accounts, backend or external assets.

## Live demo

https://y-kal.github.io/careflow-demo/

## Run

Open `index.html` in a modern browser. For consistent localStorage behavior, serve the folder with any static server, e.g. `python3 -m http.server 8000`, then visit `http://localhost:8000`. File-URL storage behavior varies by browser. If storage is blocked, mutations are refused with a visible message.

GitHub Pages serves the main branch root. All asset URLs are relative and work under a repository subpath. No API keys or environment variables are required.

## Quick demonstration

1. Initially 74 beds are occupied. Seed outcomes are 17 recovered discharges and 3 deaths: 15% mortality, 85% recovery, no warning.
2. Switch to **Admin**, open **Discharge queue**, open **CF-001**, finalize recovered discharge. Occupancy drops to 73; admission becomes available. Admit a fictional patient; the freed bed is assigned.
3. Switch to **Nurse**, search **CF-003**, record today's temperature. Reopening the record shows that a duplicate same-day entry is blocked.
4. Switch to **Doctor**, open CF-003, add a review note and approve discharge. The admin queue updates automatically. The bed stays occupied until finalization. CF-002 is already eligible; CF-005 has fever; CF-007 has a missed day.
5. Try reviewing a patient whose temperature is due. Review and approval are blocked until today's temperature exists.
6. As Doctor, expand **Record fictional death**, enter a note, type the exact patient ID and confirm. This resolves the case, releases its bed and updates outcome percentages.
7. Inspect **Audit trail**. Use **Reset / seed demo** to erase changes and recreate the starting data with dates relative to today.

## Rules

- Nurse observations: one daily reading per patient, 34–43 °C inclusive. Fever starts at 38 °C.
- Doctor review requires today's temperature and a note. A second daily review is blocked; discharge approval may include a further note. Death recording is a separate outcome action requiring a note, exact-ID confirmation, and browser confirmation.
- Discharge eligibility conservatively requires four consecutive daily readings, including today, all below 38 °C and spanning at least 72 elapsed hours. Fever, a missing day, insufficient elapsed time or a resolved case prevents eligibility. Calendar days use browser-local time; timestamps are ISO instants. DST can extend the required window beyond four readings.
- Doctor approval automatically creates an admin queue item. New observations invalidate previous approval. Admin finalization requires today's approval and doctor review, and rechecks current eligibility; only finalization records recovery and releases the bed.
- Admissions are blocked at 74 occupied beds. Death also releases a bed. Resolved records are read-only.
- Mortality is deaths / (deaths + recovered discharges); recovery is its complement. Active cases and pending approvals are excluded. No resolved cases displays a dash. Warning only when mortality is strictly above 15%.
- Role checks are repeated in the domain logic; they are **demo-only permissions, not authentication or security**.

## Persistence and limitations

**Fictional demo only. Never enter real patient data. This is not a clinical or medical decision tool.** Data stays in this browser's localStorage under `careflow.demo.v1`. There is no network transmission, sync, server, authentication or tamper-proof audit. Anyone with browser access can switch roles or edit stored data. Clearing storage removes the demo. Different devices, origins and browser profiles have separate data. Cross-tab changes trigger a refresh, but there is no transactional multi-user concurrency guarantee. Use one active editing tab.

Data is rendered through textContent / DOM creation; user names and notes are never interpolated into HTML. Role changes and navigation are UI events; patient mutations and seed/reset are reflected in the local audit (reset starts a new log).

## AI use and verification

The prototype was generated with ChatGPT Codex, then independently reviewed and tested. Review fixes add duplicate-doctor-review protection, current-day approval/review checks for finalization, and a stacked mobile layout.

Run `node core.test.cjs` (Node.js is only needed for tests). The included `test-results.txt` records 22 passing domain tests. Independent browser checks passed eight scenarios: temperature entry, doctor review and approval, admin discharge, admission and safe literal text rendering, persistence after reload, death/outcome warning, and 390px / 320px layout checks. Desktop, patient dialog, and mobile screenshots were visually inspected. JavaScript syntax checks pass.

The public deployment bundles CSS and JavaScript into `index.html`, so no installation or build step is required. The reviewed source archive also has separate source files and domain tests.
