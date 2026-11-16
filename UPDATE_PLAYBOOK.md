# Update Summary — refresh playbook

Instructions for each scheduled refresh (Mon 10 AM, Wed 7 AM, Fri 7 AM Central) of `index.html`.

## What a run does

1. Read `sources.json`. For each source, compare `lastCommit` with the repo's current HEAD
   (`git ls-remote https://github.com/<repo> HEAD`). All source repos are public — clone with
   `GIT_LFS_SKIP_SMUDGE=1 git clone --depth 20`.
2. For each changed source only: clone it, read the new figures, and update **that item** (matched by
   `item` title) and its row in the metrics table (`health`) inside the first report in `REPORTS`.
   - Most dashboards render in the browser. Serve the repo locally (`python3 -m http.server`) and read
     the rendered page with Playwright; some embed data in `const` blocks or a `data.json`.
   - Update the item's numbers and its link's `asOf` date. Keep the user's own report bullets.
   - For "Average Work Order Age", append the new week to both `charts` series and drop points older
     than 90 days; recompute the 90-day change in both metrics rows.
3. If nothing changed, change nothing and do not commit.
4. Check the page: extract the last `<script>` block and run `node --check`; open it in Playwright and
   confirm no page errors.
5. Commit `index.html` and the updated `sources.json` (new `lastCommit` values and `lastChecked`) to
   `main` and push.
6. Send the user a short summary: which sources changed, the headline number changes, and anything
   that conflicts with other items or the user's report.

## Rules (decided with the user)

- **Buckets are the user's call.** Never move an item to a different bucket. New repos found on their
  own are not added — mention them in the summary instead.
- **Data only from the sources.** No invented owners, impacts or targets. Statuses: Complete, Live,
  In Progress, Waiting, Upcoming.
- **Personnel Updates come only from the owner.** Never add, change or remove them in a scheduled run, and
  never pull personal information from source repos (the page is public on GitHub Pages).
- Label different-scope figures so they are not compared directly: facility CAPEX vs total CAPEX
  (facility + fleet), Truck Utilization's $/mile (fleet charge + fuel) vs Fleet CPM all-in $/mile.
- Flag, don't silently fix, conflicts — e.g. the 2026 fleet budget ($3,299,000 in the report vs
  $3,464,000 in the Fleet Spend YOY report), Canyon Lake finish (Project Summary vs contractor
  schedule).
- Note when a figure comes from a Saturday or a partial period.
- Keep the newest report's header date automatic (it shows the viewing date).
- Merged items stay merged: PM + Registration & Inspection; Facility + Fleet WO age;
  Store + Distribution forklift utilization.

## Not covered by a repo

- The **2026 CAPEX spent** tile (facility CAPEX from the Facility Spend Dashboard + fleet spend
  provided by the user). Recompute the total when the Facility Spend Dashboard changes; the fleet
  figure only changes when the user provides one.
- Items from the user's written report (budget meeting, internal teams, flat rate notes, Fexa follow-up
  automation plan) change only when the user sends a new report.
