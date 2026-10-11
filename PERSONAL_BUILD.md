# Personal build: what this branch adds to upstream

`personal` (seanth/designcraft) is upstream `storytold/designcraft` `main` plus the fixes below,
which are not upstream yet. This file lives only on this branch. Update it whenever something is
added, or whenever upstream merges one of these items and it can be dropped from here.

Last updated: 2026-10-11. Based on upstream `main` at `ca343e8`.

## How the branches fit together

| Branch | What it is |
|---|---|
| `main` | An exact mirror of upstream `main`. Update it only with GitHub's **Sync fork**, and never commit to it. |
| `personal` | This build: upstream `main` + every item below. |
| `drop-caps` | Upstream `main` (as of 2026-10-10) + #83 + the drop cap point-size fix. |
| `drop-cap-point-size` | aiongg's `feat/layout-parity` (#83) + the point-size fix only, for a PR to aiongg. |
| `macos-open-documents` | Upstream `main` + Finder/Dock opening, for a PR to storytold. |
| `header-fixes` | Upstream `main` + #301 + #106's fit commit + the running-header fix. |
| `running-header-page-index` | Upstream `main` + the running-header fix only, for a PR to storytold. |
| `paste-without-formatting` | #349 + the Paste without Formatting fix, for the #349 author. |
| `main-2026-10-10` | Archive of the fork's old `main` (superseded history; safe to delete). |

Each fix lives on its own branch, based on upstream `main` (or on the PR it extends), and is
merged into `personal` from there. Never open an upstream PR from `personal` or `main`.

To bring in new upstream work: **Sync fork** on `main`, then merge `main` into `personal`.

## What `personal` contains

### From other people's open upstream PRs

| Item | Source | Fixes | Notes |
|---|---|---|---|
| Drop caps, first baselines, Jump Object wrap, IDML defaults | [#83](https://github.com/storytold/designcraft/pull/83) by aiongg (`feat/layout-parity` at `a2ff7d0`, 2026-10-10), via `drop-caps` | Drop caps from IDML files, Drop Cap Style, Align Left Edge, right-to-left caps | 4 newer #83 commits (Em Box Height first baseline, `f386a98`…`0de31ef`) are not included. |
| IDML text variables | [#301](https://github.com/storytold/designcraft/pull/301) by aiongg | Running headers and other variables were dropped on IDML import | Merged whole (1 commit). |
| Lines fit by their baseline | [#106](https://github.com/storytold/designcraft/pull/106) by skylar taylor-barrick, **one commit only** (`8d56e87`, here `5d8806c`) | [#180](https://github.com/storytold/designcraft/issues/180): text in tight frames (e.g. a "Page N" header) was overset and invisible | #106 is stacked on 27 other commits (#83, #84, #81…), which are left out. Adapted for current `main` (vertical text is now a story property). |
| macOS text clipboard | [#349](https://github.com/storytold/designcraft/pull/349) by ironinthesoul | [#201](https://github.com/storytold/designcraft/issues/201): Copy/Cut/Paste between a text frame and other Mac apps | Replaces #213 (closed upstream; its fallback never ran). |

### Our own fixes

| Item | Branch / commit | Fixes | Upstream status |
|---|---|---|---|
| Drop caps scale with their point size | `drop-cap-point-size` (`732b6dc`); in `personal` as `9bf9f74` | #83 sized every cap the same whatever its point size. InDesign scales by cap size ÷ body size, keeping the baseline (checked against an InDesign capture: 2.04× vs 2.03× the line gap). The cap's highlight and caret cover a cap that rises above the line. | For a PR into aiongg's `feat/layout-parity` (#83). |
| Table-style defaults test reads `row_fills` | in `personal` via `drop-caps` (`a3530ff`) | #83's test broke after upstream changed how table styles store alternating fills | Mentioned to aiongg; needed when #83 is merged with current `main`. |
| macOS: open documents from Finder and the Dock | `macos-open-documents` (`0a695d4`, `0d46b57`) | [#211](https://github.com/storytold/designcraft/issues/211): double-click, drop on the app icon and Open With didn't open files | **Waiting on a maintainer decision:** it needs `unsafe` (CLAUDE.md forbids it; see [#259](https://github.com/storytold/designcraft/issues/259)). The `unsafe` is confined to one macOS-only module. Also check [#417](https://github.com/storytold/designcraft/pull/417), which claims #211 too. |
| Parent-page running headers by page index | `running-header-page-index` (`f23fa33`) | Running headers were empty on pages whose page number repeats an earlier one (two sections both numbered from 1) | PR to storytold `main` to open (description drafted). Needs #301 for IDML files. |
| Keep-lines test expectations | `ce4a3bc` (`personal` only) | #83's two Keep Lines Together tests expect a 6/4 split; with #106's fit rule it is 7/3 | Whichever of #83 and #106 lands second upstream will hit this. |
| Paste without Formatting reads the system clipboard | `paste-without-formatting` (`3838e59`); in `personal` as `0014059` | ⇧⌘V (or the menu item) from another app still failed after #349 | Offer to the #349 author as a follow-up. |

## Problems found but not fixed

- **"Page N" header text** (Def-e-nish-en-er-e IDML): needs the BarryGothicC font installed.
  Without it the fallback font's ascent puts the first baseline below the 11 pt frame. Should
  work on the Mac with the font; not yet confirmed.
- **Dictionary page layout differs from InDesign** (Def-e-nish-en-er-e, page 16): the left column
  starts halfway down and the footnotes sit differently, so the running headers pick different
  entries ("Earresponsibility … Dullard" vs InDesign's "Dullard … Fetidfeticide"). Not investigated.
- **Copying objects between DesignCraft and other apps** isn't supported (#349 leaves it out on
  purpose). Text copy and paste works.
- **Running headers pick entries by height first, then left to right.** Matches InDesign on normal
  pages; unverified when columns start at different heights.

## Not yet verified

- **The macOS build**, for everything macOS-specific: Finder/Dock opening, clipboard, Paste without
  Formatting. Test on the Mac:
  - Double-click an `.idml` file; drop one on the Dock icon; use Open With.
  - ⌘C/⌘X/⌘V and ⇧⌘V between TextEdit and a text frame, by shortcut and from the Edit menu.
- **The web build** (`cargo xtask wasm`) wasn't run: the build environment couldn't download the
  wasm toolchain.
- Everything else passed: fmt, clippy `-D warnings`, all workspace tests, `xtask layers` and
  `xtask assets` on Linux.

## Removing an item when upstream merges it

1. **Sync fork** on `main`, then merge `main` into `personal`.
2. Where both sides changed the same code, keep upstream's version and drop ours.
3. Remove the item's row from this file, and delete its branch once nothing else needs it.
