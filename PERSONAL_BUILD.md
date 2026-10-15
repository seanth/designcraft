# Personal build: what this branch adds to upstream

`personal` (seanth/designcraft) is upstream `storytold/designcraft` `main` plus the items below,
which are not upstream yet. This file lives only on this branch. Update it whenever something is
added, or whenever upstream merges one of these items and it can be dropped from here.

Last updated: 2026-10-15 (Save documents as setting added). Based on upstream `main` at `a930276` (0.8.0 plus 415 commits of merged
community PRs).

`personal` is rebuilt from upstream `main` whenever upstream merges something it carried (on
2026-10-13, 2026-10-14 and 2026-10-15). Each rebuild is joined to the previous `personal` history
with a merge that keeps the new content, so `git pull` still works and nothing needs a reset.

## How the branches fit together

| Branch | What it is |
|---|---|
| `main` | An exact mirror of upstream `main`. Update it only with GitHub's **Sync fork**, and never commit to it. |
| `personal` | This build: upstream `main` + every item below. |
| `drop-cap-size` | Upstream `main` + the drop cap point-size fix (one commit), for a PR to storytold. |
| `macos-open-documents` | Upstream `main` + Finder/Dock opening, for a PR to storytold. |
| `font-rescan` | Upstream `main` + #315 + the rescan when the window regains focus. |
| `paste-without-formatting` | Upstream `main` (with #349) + our Paste without Formatting fix. Probably redundant; see below. |
| `save-format` | Upstream `main` + the Save documents as setting (DesignCraft or IDML). **Personal only**, not meant for upstream. |
| `drop-cap-point-size` | Superseded by `drop-cap-size`. Safe to delete. |

Other people's PRs (#369, #635, #301) have no branch here: `personal` merges them straight from
upstream (`refs/pull/N/head`), with the conflict resolutions recorded in those merge commits.

Each fix lives on its own branch, based on upstream `main`, and is merged into `personal` from
there. Never open an upstream PR from `personal` or `main`. Every branch above is synced with
upstream `a930276` and passes the checks on its own.

To bring in new upstream work: **Sync fork** on `main`, then merge `main` into `personal`. When
the workspace version changes (0.8.0 → 0.9.0…), `macos-open-documents` needs a one-line
Cargo.lock commit for its crate's version; merge it into `personal` from there.

## What `personal` contains

### From other people's open upstream PRs

| Item | Source | Why it's here | Notes |
|---|---|---|---|
| Copied objects paste on the spread in view | [#369](https://github.com/storytold/designcraft/pull/369) by noah1400 (`dfdc650`) | Paste and Paste in Place always put objects on page 1, whatever page you were looking at (part of [#348](https://github.com/storytold/designcraft/issues/348)). | Two fixes needed with current upstream; see "Merge fixes" below. |
| Double-clicking a parent in the Pages panel shows that parent; parent thumbnails | [#635](https://github.com/storytold/designcraft/pull/635) by aiongg (`4e7b336`), added 2026-10-15 | [#624](https://github.com/storytold/designcraft/issues/624): double-clicking any parent always opened the first one, and every parent row stayed highlighted. Parents are now named by id, full name or a unique prefix (an unknown name is an error instead of quietly picking page 1's parent). | 520 commits behind upstream; 3 conflicts, see "Merge fixes". Its own tests pass. |
| IDML text variables | [#301](https://github.com/storytold/designcraft/pull/301) by aiongg (`2b8dc2b`) | Running headers and other variables were dropped on IDML import (the Def-e-nish headers). | Merged whole. Was on `header-fixes`, now deleted; its merge with current upstream (`08b049e`) and one fix (`eedf600`: its XMP dates read through upstream's size-capped package reader) are in `personal`'s history. |
| Retry missing font fallbacks after a rescan | [#315](https://github.com/storytold/designcraft/pull/315) by sanyamsingh001 (`7d16c14`), via `font-rescan` | Characters no font covered stayed missing after new fonts were found. | — |

### Our own fixes

| Item | Branch / commit | Fixes | Upstream status |
|---|---|---|---|
| Drop caps scale with their point size | `drop-cap-size` (`54305b1`) | A drop cap came out the same size whatever its point size. InDesign scales by cap size ÷ body size, keeping the baseline (checked against an InDesign capture). The cap's highlight and caret cover a cap that rises above the line. Its test fails on upstream and passes with the fix. | PR to storytold to open. |
| macOS: open documents from Finder and the Dock | `macos-open-documents` (`0a695d4`, `0d46b57`, lockfile `43c1f1b`) | [#211](https://github.com/storytold/designcraft/issues/211): double-click, drop on the app icon and Open With didn't open files. | **Waiting on a maintainer decision:** needs `unsafe` (see [#259](https://github.com/storytold/designcraft/issues/259)); confined to one macOS-only module. Sits next to upstream's cancelable Dock Quit hook; both run before the app starts. |
| Fonts installed while running are found when the window regains focus | `font-rescan` (`55f39ad`) | [#547](https://github.com/storytold/designcraft/issues/547) (same as [#328](https://github.com/storytold/designcraft/issues/328)): a font installed while DesignCraft ran needed a restart. | PR to open; reference #328. |
| Paste without Formatting reads the system clipboard | `paste-without-formatting` (`3838e59`) | ⇧⌘V from another app failed after #349. | **Probably redundant:** #349 and #84's clipboard code are both upstream now, and upstream reads the system clipboard for Paste without Formatting while typing. To decide: on plain upstream, copy text in TextEdit, then in a text frame press ⇧⌘V and also choose Edit › Paste without Formatting. If both paste plain text, delete the branch and drop it from here. |

### Personal-only features (not for upstream)

| Item | Branch / commit | What it does |
|---|---|---|
| Save documents as: DesignCraft or IDML | `save-format` (`7f209e2`) | Preferences › File Handling › Saving › **Save documents as**. With **InDesign Markup (.idml)** chosen: Save suggests `Title.idml` for a new document (a name typed without an extension gets `.idml`), and an opened `.idml` file is the document's file, so ⌘S writes IDML back to it instead of asking where to save. Save, Save As and Save a Copy write whatever format the file name says, so an old `.designcraft` file still saves as itself. IDML saves embed the images, so the file stands alone. With **DesignCraft (.designcraft)** chosen (the default), everything is as upstream. The choice is kept in `prefs.json` (`saveFormat`). |

**What IDML can't hold** is lost on every IDML save, without a warning, by design of this setting.
Known examples: placed Word/RTF/text files stay in the document but lose their link (#610), and
anything newer than DesignCraft's IDML export (some paragraph and table settings still being added
upstream). A layout that also lives in InDesign is unaffected; one that uses DesignCraft-only
features should be checked after reopening.

The web build is unchanged: its Save downloads a `.designcraft` file.

### Merge fixes

#369 with current upstream:
- **`spread_param`** returns an error for a bad spread (upstream `3d59dd5`): #369's paste
  propagates it.
- **Paste in Place sent `spread: null`** when no spread was given. Upstream rejects that, so Paste
  in Place failed. It now passes a spread only when one is given; #369's own tests caught it.
  Worth telling noah1400.

#635 with current upstream:
- `layout.pages.insert` keeps upstream's overflow-safe `after` with #635's stricter parent lookup.
- Pages panel thumbnails use upstream's capped texture loader with #635's texture names.
- Both added a `tests` module to the Pages panel; #635's is `parent_tests`.
- #635's `layout.parents.edit` extends upstream's command of the same name (`on` as before, plus
  `parent`), so they merged into one.

#301 with current upstream: its text variables and package dates sit beside upstream's Japanese
composer and table of contents in the IDML importer; a document gets both.

## Merged upstream (no longer needed here)

| Item | Upstream |
|---|---|
| Small frames (tombstones) set beside text wrap (ours) | [#589](https://github.com/storytold/designcraft/pull/589), merged 2026-10-15 (`1495575`); `small-frame-wrap` deleted |
| macOS text clipboard | [#349](https://github.com/storytold/designcraft/pull/349) by ironinthesoul, merged 2026-10-15 (`07ed400`) |
| A line is set when its baseline fits (what we took #106 for) | Upstream's own `d52ef6b6` (moppeeer), 2026-10-15, which also resized the keep-lines test frames; #106's commit and our keep-lines test update dropped |
| Text editing, threading by hand, paste, 2–5 click selection (3 clicks = line) | [#84](https://github.com/storytold/designcraft/pull/84) by aiongg, merged 2026-10-13 as `0f4f382` |
| Drop caps, first baselines, Jump Object wrap, IDML defaults | [#83](https://github.com/storytold/designcraft/pull/83) by aiongg, inside `0f4f382` (our point-size fix is not; see `drop-cap-size`) |
| Parent-page running headers by page index (ours) | [#523](https://github.com/storytold/designcraft/pull/523), merged 2026-10-12 as `a21e8db` |

## Problems found but not fixed

- **"Page N" header text** (Def-e-nish IDML): needs the BarryGothicC font installed. Without it
  the fallback font's ascent puts the first baseline below the 11 pt frame. Should work on the Mac
  with the font; not yet confirmed.
- **Dictionary page layout differs from InDesign** (Def-e-nish, page 16): the left column starts
  halfway down and the footnotes sit differently, so the running headers pick different entries.
  Not investigated.
- **Copying objects between DesignCraft and other apps** isn't supported. Text copy and paste works.
- **Running headers pick entries by height first, then left to right** (#301's logic). Confirmed
  wrong when columns start at different heights: on 2026-10-15 the Def-e-nish page 16 left column
  starts mid-entry, so its first headword ("Erythrophobia") sits below the top of the right column
  and the header reads "Fipolomist … Finna". InDesign takes the first and last entries in text
  order. Worth reporting on #301 (or fixing in a follow-up).

## Not yet verified

- **The macOS build**, for everything macOS-specific. Test on the Mac:
  - Double-click an `.idml` file; drop one on the Dock icon; use Open With. Then quit from the Dock
    with an unsaved document: the Save/Don't Save/Cancel prompt should appear.
  - ⇧⌘V and Edit › Paste without Formatting from TextEdit into a text frame, on plain upstream
    (to decide about `paste-without-formatting`).
  - Paste while looking at page 5: the objects land on page 5.
  - Pages panel: with several parents, double-click the third: the canvas shows that parent and
    only its row is highlighted.
  - With DesignCraft open, install a font in Font Book, switch back: the font is in the font menu.
  - A drop cap set larger than the body text is taller than one set at the body's size.
  - Save documents as IDML: set it in Preferences › File Handling; open an `.idml`, edit, ⌘S, and
    reopen it in DesignCraft (and in InDesign): the edit is there. A new document's ⌘S suggests
    `Untitled-1.idml`.
- **The web build** (`cargo xtask wasm`) wasn't run: this environment can't download the wasm
  toolchain.
- Everything else passed on Linux, on `personal` and on each branch above separately: fmt,
  clippy `-D warnings`, all workspace tests, `xtask layers` and `xtask assets`. On the Samhain IDML
  21 stories are overset (23 before today's upstream; 54 on upstream before #589) and no
  tombstone is.

## Removing an item when upstream merges it

1. **Sync fork** on `main`, then merge `main` into `personal`.
2. Where both sides changed the same code, keep upstream's version and drop ours.
3. Remove the item's row from this file, and delete its branch once nothing else needs it.
