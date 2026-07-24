---
portfolio: v1
project: "qualtrics-backup-site"
group: platform
source_of_truth: "PROJECT_TIMELINE.md"
status: built-awaiting-review
one_line: "Public sharing SITE (single static index.html + the downloadable Shiny-tool zip) for the Qualtrics-backup tool, for any Israeli research lab"
next_action: "Deploy to https://elad-refoua.github.io/qualtrics-backup/ (gh-pages push) once Elad OKs going live"
waiting_on: ["Elad: OK to deploy live"]
deadline: null
last_updated: 2026-07-22
priority: background
priority_source: "proposed-2026-07-24"
parent: null
---

# qualtrics-backup — sharing site for the Qualtrics-backup tool

A single static Hebrew-first (RTL) page that presents and hands out the Qualtrics-backup
Shiny tool: what you get, install-in-3-minutes, the run flow, troubleshooting, FAQ, and
an English quick summary. Zero JS, no frameworks, one accent color matching the tool's own
UI. Ships the downloadable artifact `qualtrics_backup_shiny.zip` alongside `index.html`.
This is the SHARE surface; the tool itself lives in the main `share/` project.

## Current state (understood from BUILD_NOTES.md)
- Built 2026-07-16 by the site-builder subagent; every claim traced to read sources
  (share/README.md, START_HERE.html, PROJECT.md, DIAGNOSIS_2026-07-16.md).
- 2026-07-22 content pass (Elad): QSF clarified as "structure only, not responses",
  removed the "what's new in v2" section (renumbered 01-06), added the two ways to get
  responses for shared-not-owned surveys, de-pomped the copy; **bundled START_HERE.html
  re-synced and the zip rebuilt with only that file changed** (other 9 byte-identical).
- Verified deterministically: HTML tag-balance 0 mismatches, all local assets present,
  zip SHA-256 site-copy == source-copy. Current zip = 87,474 bytes, SHA-256
  c712f5e8...f47fa.
- **Not deployed yet** — this repo built/updated the static site only. Download link is
  relative so it works as-is on GitHub Pages.
- Git repo WITH remote: github.com/elad-refoua/qualtrics-backup, last commit 79b458b
  ("Deploy: content fixes...") 2026-07-22.

## Key paths
- `index.html`, `qualtrics_backup_shiny.zip`, `assets/*.jpg`, `BUILD_NOTES.md`

## Next
Push to gh-pages to go live (per house rule: deploy by default once verified). Optional:
a fresh post-v2 screenshot of the RESPONSE BACKUP SUMMARY block to strengthen the page.
