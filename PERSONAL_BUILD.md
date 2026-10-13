# Personal build: what this branch adds to upstream

`personal` (seanth/designcraft) is upstream `storytold/designcraft` `main` plus the items below,
which are not upstream yet. This file lives only on this branch. Update it whenever something is
added, or whenever upstream merges one of these items and it can be dropped from here.

Last updated: 2026-10-13. Based on upstream `main` at `e862e0a` (v0.7.1 plus the first batch of
merged community PRs).

On 2026-10-13 `personal` was rebuilt from upstream `main` rather than updated in place: upstream had
reworked code that our old click selection, #83 and #84 all touch, and taking in #84 meant
resolving those once, cleanly. The rebuild was then joined to the old `personal` history with a
merge that keeps the new content, so `git pull` still works and nothing needs a reset.

## How the branches fit together

| Branch | What it is | In `personal`? |
|---|---|---|
| `main` | An exact mirror of upstream `main`. Update it only with GitHub's **Sync fork**, and never commit to it. | Base |
| `personal` | This build: upstream `main` + every item below. | — |
| `header-fixes` | Upstream `main` + #301 + #106's fit commit. | Yes |
| `macos-open-documents` | Upstream `main` + Finder/Dock opening, for a PR to storytold. | Yes |
| `drop-cap-point-size` | aiongg's #83 (as of `a2ff7d0`) + the point-size fix, synced with upstream; for a PR to aiongg. | Yes (the fix only: #84 already brings #83) |
| `drop-caps` | Upstream `main` + #83 + the point-size fix. | **No:** #84 contains #83, so `personal` takes #83 from #84. Keep only if you want #83 without #84. |
| `small-frame-wrap` | Upstream `main` + the tombstone fix ([#589](https://github.com/storytold/designcraft/pull/589)). | Yes |
| `font-rescan` | Upstream `main` + #315 + the rescan when the window regains focus. | Yes |
| `paste-without-formatting` | #349 + the Paste without Formatting fix. | Yes |
| `main-2026-10-10` | Archive of the fork's old `main` (superseded history). | No; safe to delete |

#84 and #369 have no branch of their own here: `personal` merges them straight from upstream
(`refs/pull/84/head`, `refs/pull/369/head`).

Each fix lives on its own branch, based on upstream `main` (or on the PR it extends), and is
merged into `personal` from there. Never open an upstream PR from `personal` or `main`.

To bring in new upstream work: **Sync fork** on `main`, then merge `main` into `personal`.

## What `personal` contains

### From other people's open upstream PRs

| Item | Source | Why it's here | Notes |
|---|---|---|---|
| Text editing, threading by hand, paste (and all of #83) | [#84](https://github.com/storytold/designcraft/pull/84) by aiongg (`feat/text-editing-and-frames` at `dd044dd`) | 2–5 click selection as in InDesign (word, line, paragraph, story); threading frames by hand (load the text cursor from a port, click to thread, Shift-click autoflow; `text.thread`/`text.unthread`); text pasted from other apps becomes a new frame and keeps its formatting; Ctrl+V pastes objects on Linux/Windows; Bold/Italic use the document's own fonts; margin/baseline-grid snapping. Includes #83: drop caps, first baselines, Jump Object wrap, IDML defaults. Fixes [#23](https://github.com/storytold/designcraft/issues/23) (and our [#546](https://github.com/storytold/designcraft/issues/546), closed as its duplicate). | 95 commits behind upstream when merged, 18 files in conflict. Resolved as aiongg did in his integration branch (`aiongg/next`), where he had one; see "Merge decisions" below. |
| Copied objects paste on the spread in view | [#369](https://github.com/storytold/designcraft/pull/369) by noah1400 (`dfdc650`) | Paste and Paste in Place always put objects on page 1, whatever page you were looking at (part of [#348](https://github.com/storytold/designcraft/issues/348)). | Conflicts with #84's clipboard step resolved as aiongg did (6d30b39). Needed one fix with current upstream; see below. |
| IDML text variables | [#301](https://github.com/storytold/designcraft/pull/301) by aiongg (`2b8dc2b`), via `header-fixes` | Running headers and other variables were dropped on IDML import (the Def-e-nish headers). | Merged whole (1 commit). |
| Lines fit by their baseline | [#106](https://github.com/storytold/designcraft/pull/106) by skylar taylor-barrick, **one commit only** (`5d8806c`), via `header-fixes` | [#180](https://github.com/storytold/designcraft/issues/180): text in tight frames (e.g. a "Page N" header) was overset and invisible. | #106 itself is stacked on 27 other commits, which are left out. One test updated for upstream's new `PathType::end`. |
| macOS text clipboard | [#349](https://github.com/storytold/designcraft/pull/349) by ironinthesoul (`f05e8f5`), via `paste-without-formatting` | [#201](https://github.com/storytold/designcraft/issues/201): Copy/Cut/Paste between a text frame and other Mac apps. | **Probably redundant with #84**, which reads the system clipboard itself (aiongg's `next` leaves #349 out). Kept because the two don't conflict: from the macOS menu while typing, #349's path runs and #84's is skipped. Drop it if the Mac clipboard works without it. |
| Retry missing font fallbacks after a rescan | [#315](https://github.com/storytold/designcraft/pull/315) by sanyamsingh001 (`7d16c14`; its head `79601ed` adds only a docs commit), via `font-rescan` | Characters no font covered stayed missing after new fonts were found. | — |

### Our own fixes

| Item | Branch / commit | Fixes | Upstream status |
|---|---|---|---|
| Drop caps scale with their point size | `drop-cap-point-size` (`732b6dc`) | #83 sized every cap the same whatever its point size. InDesign scales by cap size ÷ body size, keeping the baseline (checked against an InDesign capture). The cap's highlight and caret cover a cap that rises above the line. | For a PR into aiongg's #83/#84. |
| macOS: open documents from Finder and the Dock | `macos-open-documents` (`0a695d4`, `0d46b57`) | [#211](https://github.com/storytold/designcraft/issues/211): double-click, drop on the app icon and Open With didn't open files. | **Waiting on a maintainer decision:** needs `unsafe` (see [#259](https://github.com/storytold/designcraft/issues/259)); confined to one macOS-only module. Sits next to upstream's new cancelable Dock Quit hook; both run before the app starts. |
| Small single-character frames (tombstones) are set beside text wrap | `small-frame-wrap` (`0635dea`, `1278957`) | A one-character frame (an article's closing tombstone) was overset whenever its spread had any text-wrap object. Only overlapping objects now wrap a frame, and the minimum gap applies only where a wrap object reaches the column. On the Samhain IDML all tombstones are set and nothing else changes. | [#589](https://github.com/storytold/designcraft/pull/589), open; `1278957` answers aiongg's review. |
| Fonts installed while running are found when the window regains focus | `font-rescan` (`55f39ad`) | [#547](https://github.com/storytold/designcraft/issues/547) (same as [#328](https://github.com/storytold/designcraft/issues/328)): a font installed while DesignCraft ran needed a restart. | PR to open; reference #328. |
| Paste without Formatting reads the system clipboard | `paste-without-formatting` (`3838e59`) | ⇧⌘V from another app still failed after #349. | Offer to the #349 author, or drop with #349 if #84 covers it. |
| Keep-lines test expectations | `f2882db` (`personal` only; was `ce4a3bc`) | #83's two Keep Lines Together tests expect a 6/4 split; with #106's fit rule it is 7/3. | Whichever of #83/#84 and #106 lands second upstream will hit this. |

### Merge decisions (where #84 met upstream)

These were made while merging, in `d0b6190` and `8bbbdef`. Each could go back to aiongg or
noah1400 as a note for their own update.

- **Click selection.** Upstream added its own multi-click selection (fc01666, fixing #622) that
  selects a *sentence* on 3 clicks. `personal` keeps #84's mapping instead, which matches what
  you measured in InDesign: 2 clicks word, 3 line, 4 paragraph with its return, 5 story. Upstream's
  `text.selectSentence`, `text.selectParagraph` and `text.selectStory` commands are still there.
  Upstream's end-to-end click test now expects line, then paragraph. This replaces our old
  `click-selection` branch (#347 + 4/5 clicks), now deleted.
- **Apply colour with only a caret in text** (`object.color`): #84's behaviour, where the colour
  applies to what you type next, as InDesign does with a swatch. Upstream refused this. The
  Eyedropper (`color.sample`) still refuses a bare caret, as upstream intends.
- **Fill/Stroke proxy:** upstream's D, Shift+X, X and / commands stay. Default and Swap act on the
  text when colours affect text (#84's Formatting Affects Text), as in `aiongg/next`.
- **#84's failing test:** it used swatches named `Cyan` and `Magenta`, which upstream now rejects.
  It now uses the default CMYK swatches, as aiongg does in `next`. This was the failed check on
  #84.
- **Paste in Place sent `spread: null`** when no spread was given (#369). Upstream now rejects
  that, so Paste in Place failed. It now passes a spread only when one is given; #369's own tests
  caught it. Worth telling noah1400.
- **`spread_param`** now returns an error for bad spreads (upstream 3d59dd5): #84's paste and
  thread commands and #369's paste propagate it.

## Merged upstream (no longer needed here)

| Item | Upstream |
|---|---|
| Parent-page running headers by page index (page name in the cache signature) | [#523](https://github.com/storytold/designcraft/pull/523), merged 2026-10-12 as `a21e8db` |
| 3, 4 and 5 click selection | Upstream fc01666 (#622), merged 2026-10-13. `personal` keeps #84's version instead (3 clicks = line); see above. |

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
    with an unsaved document: the Save/Don't Save/Cancel prompt should appear (upstream's hook and
    ours sit side by side).
  - ⌘C/⌘X/⌘V and ⇧⌘V between TextEdit and a text frame, by shortcut and from the Edit menu.
  - 2, 3, 4 and 5 clicks in a text frame: word, line, paragraph, story.
  - Threading: select a text frame, click its out port, click an empty frame.
  - Paste while looking at page 5: the objects land on page 5.
  - With DesignCraft open, install a font in Font Book, switch back: the font is in the font menu.
- **The web build** (`cargo xtask wasm`) wasn't run: this environment can't download the wasm
  toolchain.
- Everything else passed on Linux: fmt, clippy `-D warnings`, all workspace tests, `xtask layers`
  and `xtask assets`. On the Samhain IDML the same 23 stories are overset as before the rebuild
  (upstream alone: 54) and no tombstone is.

## Removing an item when upstream merges it

1. **Sync fork** on `main`, then merge `main` into `personal`.
2. Where both sides changed the same code, keep upstream's version and drop ours.
3. Remove the item's row from this file, and delete its branch once nothing else needs it.
