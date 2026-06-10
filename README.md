# MyAlice — Release Status Dashboard

A single-page, always-current view of where every MyAlice release stands — for leadership, GTM, and CS. Risk, on-time confidence, GTM/CS readiness, and rollout state, computed from live Jira data.

**Live:** https://niazmorshedx.github.io/alice-rsd-q7m3k2/

> 🔒 **Internal to MyAlice.** The page is `noindex, nofollow` and the URL is unguessable by design — please do not share it outside the company.

---

## What it shows

- **Next QA gate** — countdown to the upcoming Thursday Rollout Gate.
- **Upcoming releases** — each unreleased item with a risk badge, on-time confidence, scope-completion %, owner, slippage history, and a plain-English "why."
- **GTM & CS readiness** — a matrix of release docs / GTM briefing / phased rollout / global release status per feature.
- **In Beta / Phased Rollout** — released-but-not-archived versions, still rolling out to cohorts.
- **Globally Released (GA)** — fully shipped (archived) versions.
- **Methodology footer** — how risk and confidence are computed.

The page refetches `data.json` every 5 minutes, so an open tab stays current without a reload.

---

## Release cadence

| Day | Event |
|---|---|
| **Thursday** | **Rollout Gate** — QA + readiness gate; decides what's ready to ship |
| **Sunday** | **Release Rundown** — final rundown / go-no-go call |
| **Monday (early AM)** | **Deployment / go-live** — global, phased, and pre-beta all ship here |

To make a Monday go-live, an item must be QA-passed on staging **and** approved at the preceding Thursday Rollout Gate.

## Version state = rollout state

A Jira `fixVersion`'s state maps directly to its rollout stage:

| Jira state | Meaning |
|---|---|
| **Unreleased** | In progress — still building |
| **Released, not archived** | Pre-release / beta — in phased rollout, not yet global |
| **Archived** | Globally released (GA complete) |

---

## How the numbers are computed

- **Weighted completion** across work items: Released 1.0 · Pre-release 0.95 · QA-passed 0.85 · in QA 0.6 · code review 0.4 · in progress 0.25 · to do 0. (Story tickets sitting in TO DO are excluded — that's by design, not risk.)
- **On-time confidence** = completion − 10 × postponements − 15 if unstaged work sits within ≤4 days of the Thursday gate. Null once a date has passed (risk = MISSED, "new date pending").
- **Slippage** — postponement count, total slip days, and the full date change-log, from the snapshot history.

All figures trace to a live Jira (ENGG) read at bake time.

---

## Repo structure

```
.
├── index.html    # the dashboard (layout, styling, render logic) — zero-dependency
├── data.json     # the data payload — the only file a rebake touches
└── README.md
```

- **Hosting:** GitHub Pages, deployed from `main` / root.
- **No build step, no frameworks, no CDN** — `index.html` + `data.json` only.

## Updating the data

The dashboard is rebaked from the Alice Cowork **Dashboard Ops** runbook:

```
dash+ rebake release-status
```

That pulls fresh Jira data, recomputes the indicators, rewrites `data.json`, and pushes it here (Pages redeploys automatically in ~1 minute). Manual path: upload a new `data.json` via **Add file → Upload files → Commit to `main`**. `index.html` only changes on a design update.

---

*Maintained by Product. Data source: Jira (ENGG). Internal MyAlice tool — do not distribute the URL.*
