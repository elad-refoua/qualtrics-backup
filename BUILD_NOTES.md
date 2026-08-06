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

### 2026-08-05 (4) — proactive audit: 2 more silent-failure gaps of the same class, fixed (SHA changed)
Per Elad, ran a targeted audit (reality-check skill, scoped by hand to R idioms) of `app.R` +
`qualtrics_backup.R` for the SAME bug class as the three real-user incidents already fixed today:
overly-optimistic assumptions about the filesystem, silent failures with no visible cause. Most of
the codebase held up well - the Qualtrics API layer already checks HTTP status codes and fails
loudly; the response-count metadata fetch already keeps a survey on failure rather than silently
treating it as empty (with a logged warning), which is exactly the caution `DIAGNOSIS_2026-07-16.md`
already established. Two real gaps found and fixed:

1. **Per-survey folder creation was never checked** (`backup_one_survey()`) - unlike the
   account-root folder (which has an explicit `dir.exists()` check added in an earlier hardening
   pass), the per-survey folder's `dir.create()` result was ignored. A failure here (a plausible
   trigger: Windows' 260-character MAX_PATH limit, off by default on most machines, combined with a
   deeply-nested backup root and a long survey name) used to surface as up to 19 confusing
   per-artifact "cannot open file" errors instead of one clear reason. Now checked and returns
   `ok = FALSE` with one clear message - matching the function's existing return shape exactly, so
   nothing downstream needed to change. Verified live by forcing a genuine `dir.create()` failure
   (a file blocking the path) and confirming the new branch fires correctly. Honest note: could NOT
   reproduce the MAX_PATH scenario itself on this dev machine (long paths appear enabled here) -
   the defensive value of the fix (consistency + one clear message) stands regardless of the exact
   trigger on a given user's machine.
2. **Encryption cleanup failure was silent** (`encrypt_folder()`) - `file.remove()` on the
   intermediate unencrypted tar was never checked. `file.remove()` returns `FALSE` (not an R error)
   on failure, so a locked/permission-denied file would leave the log saying "Encrypted to ..."
   while a plaintext copy of participant data sat on disk. This is the one finding with a real
   security angle (per the repo's own security invariants). Now stops loudly with the exact both
   paths named if cleanup fails. Verified live by locking the intermediate file open and confirming
   the new check stops with the expected message.

New SHA-256: `233e0315f6731207e257e206d8931d782cf04d771049356901a059dfd72f3b8a`. New size: 91,269
bytes (89.1 KiB, displayed "89 KB"). No new troubleshooting rows added - these are internal
robustness fixes, not new user-facing symptoms to search for.

### 2026-08-05 (3) — fix trailing-space folder-creation crash + audit two more free-text fields (SHA changed)
Same real user (already past the two earlier fixes) hit a third, different failure: connected
fine, listed her 254 surveys fine, then `FATAL: Could not create the output folder: <her folder
name> /ISF/2026-08-05`. Root cause reproduced live: her typed backup-root folder name almost
certainly ends in a trailing space (easy to add by accident - autocomplete, paste, a stray
keystroke). Confirmed deterministically that `dir.create()` on Windows returns `FALSE` for a path
whose last segment ends in a space, even with `recursive = TRUE`, with zero explanation - exactly
this error and nothing else.

Interesting: the codebase already defends against this for SURVEY names via `safe_folder_name()`
(strips trailing `.`/space - see its own comment), but the top-level `backup_root` field (raw free
text from the UI, correctly never run through `safe_folder_name()` since it legitimately contains
`:` and `/`) had no equivalent guard.

Fix: `trimws()` added once in `run_backup_session()` (the shared core - protects both the Shiny UI
and the CLI path, not just app.R) for both `account_name` and `backup_root`. Verified live:
reproduced the exact failure, then re-ran the identical scenario through the fix and confirmed it
succeeds.

While auditing for the same bug class (per Elad's request to proactively check for more), found
and fixed a related but distinct gap: `input$api_token` and `input$base_url` in the "Test
connection" handler were also used raw/untrimmed - a pasted token or URL with invisible
leading/trailing whitespace would fail with a generic "Unauthorized", matching a symptom already
in this page's own troubleshooting table with no explanation of why. Trimmed both at the point
they are captured into reactives, and made the one inconsistent raw `input$base_url` reference use
the trimmed local variable instead.

New SHA-256: `d889b0f2dc60999092c1df29ae20880ad0c6e61370f5af0e9c776d334110b30a`. New size: 90,545
bytes (88.4 KiB, still displayed "88 KB"). Could not run the full `tests_regression.R` suite (no
local QSF fixture / real AI PSYCH backup folder available) - the changed code does not touch
QSF/artifact parsing, so verified the specific behavior change directly instead (see above).

### 2026-08-05 (2) — fix "R library not writable" install failure + prominent extract warning (SHA changed)
A real user (not Elad) hit a second, different failure: she DID extract the ZIP correctly and R
WAS found correctly, but package installation failed with
`'lib = "C:/Program Files/R/R-4.6.1/library"' is not writable` / `unable to install packages` /
`Execution halted`. Root cause: her R was installed "for all users" (the Windows installer's
non-default choice, common on institutional/shared machines), which makes R's own library folder
admin-owned. `install.packages()` under a plain `Rscript` session never gets the interactive
"use a personal library instead?" prompt R normally offers - it just fails outright with no
fallback.

Fix (`share/run_app.R`, `share/install.R`, mirrored at root `run_app.R`): before installing
anything, resolve the user's personal per-version library (`Sys.getenv("R_LIBS_USER")`, with a
manual fallback if unset), `dir.create()` it if it does not exist yet, and prepend it to
`.libPaths()` so `install.packages()` targets it explicitly (`lib = .install_lib`) instead of
depending on the default library resolution order. Verified live: ran the exact fix logic against
a scratch profile and installed a real CRAN package (`digest`) into a freshly created personal
library end to end. Also wrapped the install call in `tryCatch` so a genuine remaining failure
(no internet, disk full, etc.) prints a plain-language message instead of a raw R error.

Also, per Elad: made the "extract before running" instruction more prominent — added right next to
the download button (not just buried in step 1 of the 3-step strip or the troubleshooting table).

New SHA-256: `c0d95f47748f965774cefe6db397b4fc5537c422c2f488740efc21132050f4c4`. New size: 89,627
bytes (87.5 KiB, displayed "88 KB").

### 2026-08-05 — silent-crash fix for "run from inside the ZIP" + troubleshooting update (SHA changed)
Root cause found and reproduced deterministically (see repo `PROJECT_TIMELINE.md`): if a user
opens `qualtrics_backup_shiny.zip` in Windows Explorer and double-clicks `Run Me (Windows).cmd`
WITHOUT extracting first, Windows silently copies out only that one file to a temp folder —
`run_app.R` and the rest are never there. On this class of machine, `Rscript.exe run_app.R`
against a missing file does not print a normal error; it crashes with an access violation
(0xC0000005) and zero output, so the user just sees "App stopped." with no explanation.
Reproduced 3x live with `Start-Process` + redirected stdout/stderr; confirmed the crash is
specific to a missing script file (`Rscript.exe -e "1+1"` works fine).

Fix (both launchers, `share/` + bundled in the zip):
- `Run Me (Windows).cmd` / `Run Me (Mac-Linux).command` now check that `run_app.R` exists next
  to them BEFORE calling R. If not, they print a plain-language explanation ("you ran this from
  inside the ZIP, extract it first") instead of silently crashing. Verified live: reproduces the
  clear message in the exact failure scenario, and the normal (extracted) path still proceeds to
  R unaffected.
- `share/README.md` troubleshooting table: split the old single "double-click closes" row (which
  assumed missing R) into two rows — the real missing-R symptom, and this new "found R but 'App
  stopped' with nothing else" symptom.
- This page (`index.html`): added the matching row to the troubleshooting table (04).
- Incidental: a stray `share/.claude/session-log.md` (local Claude Code session metadata, not
  meant for distribution) had been swept into the previous zip build by `share\*`. Removed before
  rebuilding; the token/path audit on the new zip confirms exactly the intended 10 files, 0
  40-char strings, 0 user-profile paths.

New SHA-256: `2bff5df18b659cbe3de89e74f857da1af82eeea51de8c4713fc0188102958ba7`. New size: 88,380
bytes (86.3 KiB, displayed "86 KB"). `_verify_site.py`'s size check was hardcoded to "85 KB" from
the last build — generalized to compare against the actual rounded size instead of a literal.

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
