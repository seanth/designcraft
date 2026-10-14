# Personal build: what this branch adds to upstream

`personal` (seanth/designcraft) is upstream `storytold/designcraft` `main` plus the items below,
which are not upstream yet. This file lives only on this branch. Update it whenever something is
added, or whenever upstream merges one of these items and it can be dropped from here.

Last updated: 2026-10-14. Based on upstream `main` at `11a51d5` (release 0.8.0).

On 2026-10-14 `personal` was rebuilt from upstream `main` again, as on 2026-10-13: upstream merged
#83 and #84 as one reworked commit (`0f4f382`), so the copies of them `personal` carried had to go.
Each rebuild is joined to the previous `personal` history with a merge that keeps the new
content, so `git pull` still works and nothing needs a reset.

## How the branches fit together

| Branch | What it is |
|---|---|
| `main` | An exact mirror of upstream `main`. Update it only with GitHub's **Sync fork**, and never commit to it. |
| `personal` | This build: upstream `main` + every item below. |
| `drop-cap-size` | Upstream `main` + the drop cap point-size fix (one commit), for a PR to storytold. |
| `drop-cap-point-size` | **Superseded by `drop-cap-size`**: the same fix on top of #83's old history, for a PR to aiongg that no longer applies. Safe to delete. |
| `header-fixes` | Upstream `main` + #301 + #106's fit commit + the keep-lines test update #106 needs. |
| `macos-open-documents` | Upstream `main` + Finder/Dock opening, for a PR to storytold. |
| `small-frame-wrap` | Upstream `main` + the tombstone fix ([#589](https://github.com/storytold/designcraft/pull/589)). |
| `font-rescan` | Upstream `main` + #315 + the rescan when the window regains focus. |
| `paste-without-formatting` | #349 + the Paste without Formatting fix. |

#369 has no branch of its own here: `personal` merges it straight from upstream
(`refs/pull/369/head`).

Each fix lives on its own branch, based on upstream `main` (or on the PR it extends), and is
merged into `personal` from there. Never open an upstream PR from `personal` or `main`. Every
branch above is synced with upstream 0.8.0 and passes the checks on its own.

To bring in new upstream work: **Sync fork** on `main`, then merge `main` into `personal`. When
the workspace version changes (0.8.0 → 0.9.0…), `macos-open-documents` needs a one-line
Cargo.lock commit for its crate's version; merge it into `personal` from there.

## What `personal` contains

### From other people's open upstream PRs

| Item | Source | Why it's here | Notes |
|---|---|---|---|
| Copied objects paste on the spread in view | [#369](https://github.com/storytold/designcraft/pull/369) by noah1400 (`dfdc650`) | Paste and Paste in Place always put objects on page 1, whatever page you were looking at (part of [#348](https://github.com/storytold/designcraft/issues/348)). | Two fixes needed with current upstream; see "Merge fixes" below. |
| IDML text variables | [#301](https://github.com/storytold/designcraft/pull/301) by aiongg (`2b8dc2b`), via `header-fixes` | Running headers and other variables were dropped on IDML import (the Def-e-nish headers). | Merged whole (1 commit). |
| Lines fit by their baseline | [#106](https://github.com/storytold/designcraft/pull/106) by skylar taylor-barrick, **one commit only** (`5d8806c`), via `header-fixes` | [#180](https://github.com/storytold/designcraft/issues/180): text in tight frames (e.g. a "Page N" header) was overset and invisible. | #106 itself is stacked on 27 other commits, which are left out. Needs the keep-lines test update below. |
| macOS text clipboard | [#349](https://github.com/storytold/designcraft/pull/349) by ironinthesoul (`f05e8f5`), via `paste-without-formatting` | [#201](https://github.com/storytold/designcraft/issues/201): Copy/Cut/Paste between a text frame and other Mac apps. | **Probably redundant now that #84 is upstream**, which reads the system clipboard itself. Kept until tested: on plain upstream 0.8.0, try ⌘C/⌘V and ⇧⌘V between TextEdit and a text frame, by shortcut and from the Edit menu. If they work, drop #349 and our Paste without Formatting fix. |
| Retry missing font fallbacks after a rescan | [#315](https://github.com/storytold/designcraft/pull/315) by sanyamsingh001 (`7d16c14`), via `font-rescan` | Characters no font covered stayed missing after new fonts were found. | — |

### Our own fixes

| Item | Branch / commit | Fixes | Upstream status |
|---|---|---|---|
| Drop caps scale with their point size | `drop-cap-size` (`54305b1`) | A drop cap came out the same size whatever its point size. InDesign scales by cap size ÷ body size, keeping the baseline (checked against an InDesign capture). The cap's highlight and caret cover a cap that rises above the line. Its test fails on upstream 0.8.0 and passes with the fix. | **Not in #83/#84 as merged** (`0f4f382`). PR to storytold to open. |
| macOS: open documents from Finder and the Dock | `macos-open-documents` (`0a695d4`, `0d46b57`, lockfile `43c1f1b`) | [#211](https://github.com/storytold/designcraft/issues/211): double-click, drop on the app icon and Open With didn't open files. | **Waiting on a maintainer decision:** needs `unsafe` (see [#259](https://github.com/storytold/designcraft/issues/259)); confined to one macOS-only module. Sits next to upstream's cancelable Dock Quit hook; both run before the app starts. |
| Small single-character frames (tombstones) are set beside text wrap | `small-frame-wrap` (`0635dea`, `1278957`) | A one-character frame (an article's closing tombstone) was overset whenever its spread had any text-wrap object. Only overlapping objects now wrap a frame, and the minimum gap applies only where a wrap object reaches the column. On the Samhain IDML all tombstones are set. | [#589](https://github.com/storytold/designcraft/pull/589), open; synced with 0.8.0 (its tests sit next to #83's, now upstream). |
| Fonts installed while running are found when the window regains focus | `font-rescan` (`55f39ad`) | [#547](https://github.com/storytold/designcraft/issues/547) (same as [#328](https://github.com/storytold/designcraft/issues/328)): a font installed while DesignCraft ran needed a restart. | PR to open; reference #328. |
| Paste without Formatting reads the system clipboard | `paste-without-formatting` (`3838e59`) | ⇧⌘V from another app still failed after #349. | Goes with #349; see its row. |
| Keep-lines test expectations | `header-fixes` (`1173bf9`; was `ce4a3bc` in `personal` only) | #83's two Keep Lines Together tests (now upstream) expect a 6/4 split; with #106's fit rule it is 7/3. | Goes with #106: whoever lands #106 upstream needs it. |

### Merge fixes (#369 with current upstream)

- **`spread_param`** now returns an error for a bad spread (upstream `3d59dd5`): #369's paste
  propagates it.
- **Paste in Place sent `spread: null`** when no spread was given. Upstream now rejects that, so
  Paste in Place failed. It now passes a spread only when one is given; #369's own tests caught
  it. Worth telling noah1400.

## Merged upstream (no longer needed here)

| Item | Upstream |
|---|---|
| Parent-page running headers by page index (page name in the cache signature) | [#523](https://github.com/storytold/designcraft/pull/523), merged 2026-10-12 as `a21e8db` |
| Text editing, threading by hand, paste, 2–5 click selection (3 clicks = line, as in InDesign) | [#84](https://github.com/storytold/designcraft/pull/84) by aiongg, merged 2026-10-13 as `0f4f382` |
| Drop caps, first baselines, Jump Object wrap, IDML defaults | [#83](https://github.com/storytold/designcraft/pull/83) by aiongg, inside the same commit `0f4f382` (our point-size fix is not; see `drop-cap-size`) |
| #84's swatch test (`Cyan`) and the merge decisions of 2026-10-13 | Settled by aiongg's own merge; no longer ours to carry |

## Problems found but not fixed

- **"Page N" header text** (Def-e-nish IDML): needs the BarryGothicC font installed. Without it
  the fallback font's ascent puts the first baseline below the 11 pt frame. Should work on the Mac
  with the font; not yet confirmed.
- **Dictionary page layout differs from InDesign** (Def-e-nish, page 16): the left column starts
  halfway down and the footnotes sit differently, so the running headers pick different entries.
  Not investigated.
- **Copying objects between DesignCraft and other apps** isn't supported. Text copy and paste works.
- **Running headers pick entries by height first, then left to right.** Matches InDesign on normal
  pages; unverified when columns start at different heights.

## Not yet verified

- **The macOS build**, for everything macOS-specific. Test on the Mac:
  - Double-click an `.idml` file; drop one on the Dock icon; use Open With. Then quit from the Dock
    with an unsaved document: the Save/Don't Save/Cancel prompt should appear.
  - ⌘C/⌘X/⌘V and ⇧⌘V between TextEdit and a text frame, by shortcut and from the Edit menu (and
    on plain upstream, to decide about #349).
  - Paste while looking at page 5: the objects land on page 5.
  - With DesignCraft open, install a font in Font Book, switch back: the font is in the font menu.
  - A drop cap set larger than the body text is taller than one set at the body's size.
- **The web build** (`cargo xtask wasm`) wasn't run: this environment can't download the wasm
  toolchain.
- Everything else passed on Linux, on `personal` and on each branch above separately: fmt,
  clippy `-D warnings`, all workspace tests, `xtask layers` and `xtask assets`. On the Samhain IDML
  the same 23 stories are overset as before (54 on upstream `main` without these fixes, measured 2026-10-12) and no tombstone is; the
  Def-e-nish running headers show each page's first and last entries.

## Removing an item when upstream merges it

1. **Sync fork** on `main`, then merge `main` into `personal`.
2. Where both sides changed the same code, keep upstream's version and drop ours.
3. Remove the item's row from this file, and delete its branch once nothing else needs it.
