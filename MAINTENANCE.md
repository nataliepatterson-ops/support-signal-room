# Support Signal Room — Maintenance Guide

**Parts:** GitHub Pages site (`index.html`) · Apps Script feed (`DataFeed.gs`, rev 5) · two source Google Sheets (Widget/Email QA + KB Scorecard).
Numbers and month labels update via **Sync live data**. Narrative text (takeaways, readouts, overview bullets) is manual.

---

## Monthly ritual (KB reporting cycle)

1. **Refresh the KB workbook raw tabs** for the new month (Deflections by Role, Search Monthly Metrics, End User Sessions, Guide Article Metrics, Raw Tickets by Group).
2. **Advance the Reporting Month cell** on the KB Scorecard tab (e.g. `2026-07-01` / `2026-06-01`). Every month label on the dashboard derives from this.
3. **Rename the segments table columns** on the Search Findability tab to the new months (e.g. `Jul Confirmed`, `Jun Confirmed`, `Jul Assumed`, `Jun Assumed`). The feed intentionally skips segments until the headers match the reporting month — this prevents stale-month regressions.
4. Open the dashboard → **Sync live data**. All KPIs, tables, charts, and month labels update.
5. **Review the narrative** against the new numbers: the 18 takeaway lines (red-arrow italics under section headers), the two management readouts, and the three Overview cards. Edit any that no longer hold.
6. **Bump `narrativeMonth`** in index.html (search for `narrativeMonth:`) to the new month. Until you do, an amber banner warns that the prose lags the data — that's the reminder, not an error.

## Deploying changes

- **Dashboard:** commit `index.html` to the repo → GitHub Pages rebuilds automatically. If the Pages build fails at "Set up job" with Service Unavailable errors, it's a GitHub Actions incident (check githubstatus.com), not your code — re-run when resolved.
- **Feed:** Apps Script → paste changes → **Deploy → Manage deployments → ✏️ Edit → Version: New version → Deploy.** This keeps the same `/exec` URL; a *New deployment* changes the URL and breaks the dashboard's `LIVE_FEED_URL`.

## Troubleshooting

- **First stop:** open the feed URL with `?debug=1`. The `errors` array names any section that failed to extract and why.
- **A section stopped updating:** the feed finds data by exact label text. If someone renames a sheet label or tab, that section silently returns null and the dashboard keeps its last-known defaults. Restore the label or update the string in DataFeed.gs.
- **"Sync failed — showing last good data":** feed unreachable or returned a fatal error. Check the /exec URL directly; confirm deployment access is "Anyone." (The dashboard loads it via JSONP because Apps Script doesn't send CORS headers — that's by design.)
- **Segments show "—":** either June-style partial role export (expected; chart falls back to last complete month), or the column headers weren't renamed (step 3).

## Feed-computed sections (empty until first sync)

Performance by topic, Conversation duration, and QA→KB pipeline statuses are tallied by the feed from the full QA sheet, conversation log, and feedback tracker — the dashboard ships them empty ("Populates on first live sync"). KB success vs tickets, Top failed queries, and Ticket drivers read the workbook's own tables (the last two auto-follow the reporting month).

**Program impact & coverage and Handoff necessity populate from the widget sheet only** — they show em-dashes / a pending note until their data exists there. For impact, add label cells `FT Coverage Rate`, `Projected Coverage Rate`, `Support Nudges per Month`, `L1 Touches Removed` (same label/value/sub layout as the headline KPIs) and the feed picks them up. For handoff necessity, add the L1 handoff-reply sample to the sheet, then mirror the extractor stub noted in DataFeed.gs. The dashboard presents no number that isn't in a source spreadsheet.

## New-metric tabs the sheet can grow into (labels the feed already watches)

- **Automation tab** (widget sheet): two columns, `Metric | Value`. Metric labels: `Auto-Reminders Sent`, `Reply Rate After Reminder`, `Auto-Closes Executed`, `Reopen Rate After Auto-Close`, `Baseline L1 Touches`, `L1 Touches Actual`, `Minutes per Touch`. Rates are percent-formatted cells. Powers the Automation section, Hours Returned, and the reopen guardrail on the Overview.
- **Widget QA Monthly tab** (widget sheet): columns `Month | Reviewed | Pass Rate | Deflection Rate`, one row per month. Powers the Monthly QA trend.
- **Impact labels** (widget Metrics tab, label/value/sub layout): `FT Coverage Rate`, `Projected Coverage Rate`, `Support Nudges per Month`, `L1 Touches Removed`, `Recontact Rate After Deflection`.
- **Email section label:** `Email Coverage Rate` (replaces the retired Review Complete KPI).
- **Customer feedback** needs no new data — computed on sync from the conversation log's `csat` / `relevance_rating` columns.

## Retired sections (Aug 2026)

Risk signal register, Partial-intake reclassification, and Ticket intake QA highlights were removed as QA-workbench content duplicating the Quality & risk KPIs; the reclassification finding lives in the changelog. The feed no longer sends them.

## Load-bearing labels — do not rename without updating DataFeed.gs

- **Widget sheet ("Metrics" tab):** `Email Ticket Intake QA Dashboard` (splits widget vs email sections); KPI labels like `Deflection Rate`, `Technical QA Pass Rate`; table headers `Category/Count/Share`, `Signal/Count/Rate`, `Risk Signal`, `Reclass Outcome`, `QA Signal`.
- **KB workbook:** tab names `KB Scorecard`, `Monthly KPI Trend`, `Support Impact Metrics`, `Content Health Metrics`, `Search Findability Metrics`, `Raw Tickets by Group`; the `Reporting Month` cell; table headers `Metric` / `Current Month` / `Previous Month` / `MoM Change`; trend month headers in `Mmm YYYY` form; `User Segment`; `Ticket group` / `Ticket created - Month` / `Created`; `Month` / `Tickets Created (support queues)` / `Total Deflections` / `KB Success Rate`; `Search Query` / `Searches with No Results` (title case — distinct from the raw search tab); `Issue Origin` / `Linked Article` / `Ticket Count`.
- **Widget sheet (computed sections):** QA table headers `Conversation ID` / `Intake Result` / `Support Type` / `KB Update Needed?`; tracker headers `Feedback Item` / `Status`; conversation-log headers `time_spent_in_widget` / `deflected`.

## Known gaps & standing caveats

- **Handoff necessity (section 04B)** is a manual 13-conversation sample living only in index.html — sync doesn't touch it. To make it live: add it to the widget sheet, then mirror the extractor stub noted in DataFeed.gs `extractWidget`.
- **No-Result Search %** is deliberately not shown — the workbook flags its definition as unverified (Zendesk report disagrees). Raw no-result counts still display.
- **June 2026 tickets-after-search** uses a different methodology than Jan–May; treat pre-June search comparisons as directional.
- New ticket **groups** added to the sheet won't appear until added to the `groups` list in DataFeed.gs (months are automatic; groups are not).
- **Array merge is replacement-based:** a synced list fully replaces the built-in one at its own length (no stale ghost rows), and a section the feed can't find is skipped entirely (defaults kept). If a fed table looks truncated, check `?debug=1` for that extractor.

## Key locations

- Feed URL: `LIVE_FEED_URL` constant near the bottom of index.html
- Sheet IDs: top of DataFeed.gs (`WIDGET_SHEET_ID`, `KB_SHEET_ID`)
- Narrative flag: `narrativeMonth` in index.html's `defaultState()`
- Theme, jump nav, tooltips, takeaways: all self-contained in index.html — no build step, no dependencies beyond the Chart.js CDN.
