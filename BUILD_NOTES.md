# BUILD_NOTES — qualtrics-backup sharing site

Built: 2026-07-16
Target deployment: https://elad-refoua.github.io/qualtrics-backup/
Builder: Claude Code (site-builder subagent), on Elad's request.

## What was built

```
site/
  index.html                   the whole site: single static page, RTL Hebrew primary,
                               English summary section at the end. No frameworks, no
                               external JS/CSS/fonts (zero JS at all), system font stack,
                               inline SVG favicon. Mobile-responsive via CSS grid + one
                               media query. One accent color (#0a5fd0) + navy hero,
                               matching the tool's own UI palette (START_HERE.html / app).
  qualtrics_backup_shiny.zip   byte-identical copy of the download artifact (verified below)
  assets/
    index_final.jpg            _index.html screenshot (what-you-get section)
    preview_top.jpg            preview.html as respondents saw it
    preview_tech.jpg           preview.html with the tech-info toggle open
    app_help.jpg               the Shiny app connect/output/run screen (use section)
    app_result.jpg             end-of-run live log + result card (use section)
  BUILD_NOTES.md               this file
```

Page sections (in order): hero + download; 10-second strip; 01 what you get
(per-survey file table + 3 screenshots); 02 install in 3 minutes (Windows + Mac
cards, R requirement, Check-R helper); 03 use (6-step run flow, resume, CLI note,
2 screenshots); 04 what's new in v2 (summary / NO_RESPONSES / retry+MISSING_
RESPONSES_README / plain-language 404-400); 05 troubleshooting (6-row table +
shared-not-owned story + token how-to with prominent warning); 06 FAQ (9 items,
pure-CSS details/summary); 07 English quick summary; bottom CTA; footer.

## Download artifact facts (displayed on the page)

- File: `qualtrics_backup_shiny.zip`
- SHA-256: `c712f5e853c48652dfd6ee9421216cbaca9ec32c54d2beefbd204d51a36f47fa`
  (source and site copy verified identical by hash; updated 2026-07-22 — see Changelog)
- Size: 87,474 bytes — displayed as "85 KB" (KiB, matching Windows Explorer display)
- Build date of the zip: 2026-07-16 (v2 rollout rebuild per DIAGNOSIS_2026-07-16.md §6)
- Version label: "2.0" (per the task spec; the codebase calls it "v2")
- Zip contents (10 files, verified by listing): app.R, Check R (Mac-Linux).command,
  Check R (Windows).cmd, install.R, qualtrics_backup.R, README.md,
  Run Me (Mac-Linux).command, Run Me (Windows).cmd, run_app.R, START_HERE.html

## Sources used (all read in full; every claim on the page traces to one of these)

1. `share/README.md` — canonical user instructions (post-v2 update of 2026-07-16).
   Source of: install steps, Run Me / Check R button names, token steps, base-URL
   guidance, run flow, resume behavior, file table, troubleshooting table,
   safety paragraph, "free to use and share" + contact.
2. `share/START_HERE.html` — Hebrew phrasings for the file glossary and steps
   (reused/adapted where they existed, to keep the site consistent with the package).
3. `README.md` (repo root) + `PROJECT.md` — what the tool is, Shiny/CLI architecture,
   read-only endpoint list, token-in-memory security model, restore instructions.
4. `DIAGNOSIS_2026-07-16.md` — all v2 behaviors: RESPONSE BACKUP SUMMARY separating
   "empty (fine)" from "HAS DATA - not backed up", `_responses_summary.txt`,
   NO_RESPONSES.txt, one retry (5 s) + MISSING_RESPONSES_README.txt, plain-language
   404/400 hints, the shared-not-owned diagnosis (metadata.json ->
   responsecounts.auditable, ownerId, owner-token fix, "- Copy" copies structure only),
   and the zip rebuild record. The "2 of 37" story in section 04 comes from here.
5. Screenshots: taken 2026-07-05 from a real run/preview (app_help, app_result,
   index_final, preview_top, preview_tech). matrix_view.jpg and start_here.jpg were
   available but not used (redundant with preview_top / with the site itself).

## Deterministic verification performed

- HTML well-formedness: Python tag-balance parser (html.parser, tracking an open-tag
  stack for all non-void elements) — 0 mismatches, 0 stray closers.
- All referenced local assets exist: every `src`/`href` pointing at a local path
  checked against the filesystem — all present.
- Zip integrity: SHA-256 of `site/qualtrics_backup_shiny.zip` equals SHA-256 of the
  source `qualtrics_backup_shiny.zip` (d207925f...).
- The SHA-256 and size displayed in the page text were cross-checked by script
  against the actual file (hash appears 2x on page, byte-identical to computed).

## Flags / honest caveats

1. **Screenshots predate v2** (taken 2026-07-05, v1-era). The UI is unchanged in v2
   except new log lines, but the app_result.jpg live log does NOT show the new
   RESPONSE BACKUP SUMMARY, and index_final.jpg shows "?" in the Responses column.
   Captions were written to stay accurate (they describe what is shown, and the
   summary is described in text only). If Elad wants, a fresh post-v2 run screenshot
   of the summary block would strengthen section 04.
2. **No formal license exists** — per instructions, the site says only
   "חינם לשימוש מחקרי / free for research use" + "free to use and share"
   (the exact phrase from share/README). No license text was fabricated.
3. **"Version 2.0" label**: the repo calls the release "v2"; the task specified
   displaying "version 2.0". Displayed as "גרסה 2.0". Date shown = zip build date
   (2026-07-16).
4. **Deployment not performed** — this task built the static site only. Deploying
   to https://elad-refoua.github.io/qualtrics-backup/ (gh-pages / repo push) is a
   separate step. The download link is relative (`qualtrics_backup_shiny.zip`), so
   it works as-is on GitHub Pages, a shared drive, or locally.
5. The base-URL example on the page is generic (`something.qualtrics.com`); the
   app's default (`biusocialsciences.eu.qualtrics.com`) was deliberately not
   presented as "the" address, since the audience is any Israeli lab.
6. Contact email shown (eladrefoua@gmail.com) is the deliberate contact per spec;
   no other personal data appears on the page.

## Changelog

### 2026-07-22 — content fixes, de-pomp, and bundled-HTML sync (SHA changed)
Site page (`index.html`), per Elad:
- QSF `survey.qsf` now states it restores the **survey structure only, not the responses**
  (file-list entry + the matching FAQ).
- Removed the "מה חדש בגרסה 2" section entirely (and the parallel English "New in v2.0"
  paragraph); renumbered the following sections to 01–06.
- Shared-not-owned survey section now gives **two ways** to get the responses: via the tool
  with the owner's token, **or** a manual Qualtrics export (works even from the shared
  account). Mirrored in the FAQ.
- De-pomped the copy (removed "עם כל מה שתצטרכו אי-פעם", "קוולטריקס תיעלם מחר…", "שאלות שכל
  מעבדה שואלת", "מוכנים לגבות?", stray "מלא"/bold emphasis; light English de-emphasis).

Bundled tool HTML (`share/START_HERE.html`, shipped inside the zip) — synced to match the
site: same QSF structure-not-responses clarification, same shared-survey manual-export note,
same de-pomp. The zip was rebuilt with **only START_HERE.html changed** (the other 9 files —
app.R, qualtrics_backup.R, README.md, run scripts — copied byte-for-byte from the prior zip).
Both the site copy and the root source copy were replaced and re-verified identical.
New size 87,474 bytes (85.4 KiB → still "85 KB"). Nothing deployed yet.
