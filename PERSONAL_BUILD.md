# Personal build: what this branch adds to upstream

`personal` (seanth/designcraft) is upstream `storytold/designcraft` `main` plus the items below,
which are not upstream yet. This file lives only on this branch. Update it whenever something is
added, or whenever upstream merges one of these items and it can be dropped from here.

Last updated: 2026-10-17. Based on upstream `main` at `b17efb2` (brought in with `inline-object-select`;
before that `c2609f9`, 0.8.0 plus aiongg's integration branch, merged upstream as PR #1139).

`personal` is rebuilt from upstream `main` whenever upstream merges something it carried
(2026-10-13, 14, 15 and 16). Each rebuild is joined to the previous `personal` history with a
merge that keeps the new content, so `git pull` still works and nothing needs a reset.

## How the branches fit together

| Branch | What it is |
|---|---|
| `main` | An exact mirror of upstream `main`. Update it only with GitHub's **Sync fork**, and never commit to it. |
| `personal` | This build: upstream `main` + the ten branches below. |
| `inline-objects-under-text` | Upstream `main` + inline objects paint under their frame's text, on screen and in PDF (one commit). No PR yet. |
| `running-header-order` | Upstream `main` + running headers read a page in text order (one commit). No PR yet. |
| `parent-page-display` | Upstream `main` + parent pages show running-header placeholders, and document-page overlays (¶ marks) stay off them (one commit). No PR yet. |
| `drop-cap-size` | Upstream `main` + the drop cap point-size fix (one commit). PR [#1184](https://github.com/storytold/designcraft/pull/1184). |
| `font-rescan` | Upstream `main` + fonts installed while running are found when the window regains focus. No PR: MaridWSH's [#1163](https://github.com/storytold/designcraft/pull/1163) (Type › Reload Fonts, manual) covers #547 and #328 and handles edited fonts better; it leaves the focus trigger for a follow-up. Once #1163 merges, rework this as that follow-up (focus runs its reload in the background) and drop our own file-stamp code. |
| `tool-units` | Upstream `main` + tool readouts (Page tool size, smart-guide distances) in the document's ruler units instead of always picas (one commit). PR [#1196](https://github.com/storytold/designcraft/pull/1196). |
| `inline-object-select` | Upstream `main` + inline (anchored) images selected and resized with the Selection tool (four commits). PR [#1195](https://github.com/storytold/designcraft/pull/1195). |
| `flexible-word-spacing` | Upstream `main` + Preferences › Composition › Word Spacing below Minimum (one commit). PR [#1190](https://github.com/storytold/designcraft/pull/1190). |
| `idml-app-icon-drop` | Upstream `main` + `.idml` files can be dropped on the macOS app icon when InDesign is installed (one commit). PR [#1183](https://github.com/storytold/designcraft/pull/1183) (a gap in #417). |
| `save-format` | Upstream `main` + the Save documents as setting (DesignCraft or IDML). Started as personal only; offered upstream as PR [#1105](https://github.com/storytold/designcraft/pull/1105). |

Each branch is based on upstream `main` and merged into `personal` from there. Never open an
upstream PR from `personal` or `main`. Every branch above is synced with upstream `c2609f9` and
passes the checks on its own.

To bring in new upstream work: **Sync fork** on `main`, then merge `main` into `personal`.

## What `personal` contains

### Our fixes (meant for upstream)

| Item | Branch / commits | Fixes | Upstream status |
|---|---|---|---|
| Inline pictures don't cover the next line | `inline-objects-under-text` (`afc6d31`) | Anchored objects were painted after all of their frame's text, so an inline picture hanging below its baseline (Def-e-nish page 7: the Flukemunschelfen picture, Y offset −17 pt) covered the top of *Foofie* with its white background. InDesign paints them under the text (its PDF draws the picture before the following lines). Renderer and PDF export now draw them after paragraph shading, before the text. Tests in both fail on upstream and pass with the fix. | Not yet offered upstream. |
| Running headers read a page in text order | `running-header-order` (`34632c0`) | *First/last on page* sorted a page's entries by height, so a page whose last entry tops column 2 (or whose column 1 starts mid-entry) named the wrong one: Def-e-nish page 2's right header showed *Archangel* (bottom of column 1) instead of *Baas Roma*. Entries now order by their story's first position on the page, then by position in the story, as InDesign does. Its test fails on upstream and passes with the fix. | Not yet offered upstream. |
| Parent pages: header placeholders, no stray ¶ marks | `parent-page-display` (`54ea155`) | On a parent page a running-header variable had no page to read and came out blank; it now shows `<variable name>`. While parents were edited, document frames weren't laid out and fell back to the canvas origin, which is the right parent page, so every page's hidden characters, spelling squiggles and similar overlays piled up there. Its canvas test fails on upstream and passes with the fix. | Not yet offered upstream. |
| Drop caps scale with their point size | `drop-cap-size` (`54305b1`) | A drop cap comes out the same size whatever its point size. InDesign scales by cap size ÷ body size, keeping the baseline (checked against an InDesign capture). The cap's highlight and caret cover a cap that rises above the line. Its test fails on upstream and passes with the fix. | Open upstream as [#1184](https://github.com/storytold/designcraft/pull/1184) (2026-10-17). |
| Fonts installed while running are found when the window regains focus | `font-rescan` (`55f39ad`, ported onto upstream's reworked scan in `0b32ed6`) | [#547](https://github.com/storytold/designcraft/issues/547) / [#328](https://github.com/storytold/designcraft/issues/328): a font installed while DesignCraft runs needs a restart. Upstream can rescan (`load_system_fonts`, with #315's retry of missing characters and #443's fonts activated by font managers), but nothing calls it while the app runs; its own `docs/gaps.md` still lists "reload when fonts change on disk (#328)". This branch rescans in the background when the window regains focus, reads only files whose size or date changed, and re-lays out text when the fonts actually changed. | Waiting on [#1163](https://github.com/storytold/designcraft/pull/1163) (manual reload, open 2026-10-17); then a focus-trigger follow-up PR. |
| `.idml` dropped on the macOS app icon opens | `idml-app-icon-drop` (`b09233a`) | Upstream's #417 declares `.idml` only by a type it imports. When InDesign is installed, its own exported type for `.idml` wins, so the declaration no longer matches: Finder refuses the drop on the app's icon and Open With leaves DesignCraft out, while Dock drops still work. Adds a second `CFBundleDocumentTypes` entry by extension (Viewer, Alternate), which matches whatever type the file has. Only `packaging/macos/Info.plist.in` changes; checked that it parses. | Open upstream as [#1183](https://github.com/storytold/designcraft/pull/1183) (2026-10-17). |
| Justified lines may squeeze word spaces below the Minimum, as InDesign does | `flexible-word-spacing` (`bf481d0`) | InDesign treats Minimum Word Spacing as a preference: it squeezes a line's spaces below it (to 25–30%) rather than set a much looser line. DesignCraft treated it as a limit, so lines broke differently (in the dictionary IDML, page 1's cat image moved to column 2 and every later page shifted). The rules were measured from InDesign's PDFs against their IDML (dictionary; Samhain story file with and without hyphenation): Single-line Composer fills to the Minimum and squeezes one more word when that falls short of Desired by less than 0.19× what the loose line exceeds it (97% of lines); Paragraph Composer costs each line ln(spacing ÷ Desired)², ×4 when squeezed (89% of paragraphs, 75% before). A document setting in Preferences › Composition: on for documents opened from IDML, off (as before) for new ones and old `.designcraft` files. The dictionary now matches 87.3% of InDesign's lines (72.5% before); page 1 now ends at *Antefa* as in InDesign. Hyphenated text still differs mostly by hyphenation points (DesignCraft and InDesign agree on ~60% of hyphenated words). | Open upstream as [#1190](https://github.com/storytold/designcraft/pull/1190) (2026-10-17). |
| Inline images can be selected and resized | `inline-object-select` (`d51a650`) | An inline or anchored image lives in its story, not on the page, so the Selection tool never found it: a click selected the text frame around it. Now a click selects it where it sits in the text and shows its handles (a drag doesn't move it; it flows with the text, as in InDesign). A handle drag shows an outline and the W/H while the image stays put (as in InDesign); on release the frame takes the new size, cropping the picture from the sides that moved (⌘-drag scales it instead; Shift keeps proportions), and the text places it again; one undo step. Delete removes it from the text; Anchored Object Options and Release act on it. New commands `anchored.select`, `anchored.resize`. Not yet: objects in footnotes or table cells, objects turned with their frame, and the other object commands (fitting, stroke and fill, Direct Selection of the picture). | Open upstream as [#1195](https://github.com/storytold/designcraft/pull/1195) (2026-10-17). |
| Tool readouts use the document's units | `tool-units` (`3eb7b50`) | The size and distance readouts that follow the pointer (Page tool, smart guides) took their unit from a UI setting that nothing changes from picas, so a document in inches read in picas. They now use the active document's ruler units, as the Control and Properties fields do. | Open upstream as [#1196](https://github.com/storytold/designcraft/pull/1196) (2026-10-17). |

### Personal feature (also offered upstream as #1105)

| Item | Branch / commits | What it does |
|---|---|---|
| Save documents as: DesignCraft or IDML | `save-format` (`7f209e2`; web `2477a82`; on #417 `09d2842`) | Preferences › File Handling › Saving › **Save documents as**. With **InDesign Markup (.idml)** chosen: Save suggests `Title.idml` for a new document (the save dialog filters on `.idml` and adds it to a name typed without it), and an opened `.idml` file is the document's file, so ⌘S writes IDML back to it instead of asking where to save. Save, Save As and Save a Copy write whatever format the file name says: a `.idml` name stays `.idml` (upstream would otherwise add `.designcraft`), any other name gets upstream's `.designcraft` rule. IDML saves embed the images. With **DesignCraft (.designcraft)** chosen (the default), everything is as upstream. Kept in `prefs.json` (`saveFormat`). |

With IDML chosen, an opened file is only treated as the document's file when it is **named**
`.idml` and didn't come through upstream's InDesign converter. A converted `.indd`/`.indt`, or an
IDML file recognised by its content but named otherwise, opens unsaved, so Save never overwrites
it.

**What IDML can't hold** is lost on every IDML save, without a warning, by design of this setting.
Known examples: placed Word/RTF/text files stay in the document but lose their link, and anything
newer than DesignCraft's IDML export. A layout that also lives in InDesign is unaffected; one that
uses DesignCraft-only features should be checked after reopening.

**In the web build**, Save is a download: `Title.idml` (an IDML package) with InDesign Markup
chosen, else `Title.designcraft`. A browser can't write back to the file it opened, so each Save
is a new download. The web app keeps the Preferences dialog's settings in the browser's local
storage, as the desktop app keeps `prefs.json`. You reported your builds working on 2026-10-15.

## Merged upstream (no longer needed here)

| Item | Upstream |
|---|---|
| macOS: open documents from Finder and the Dock (our `macos-open-documents`) | aiongg's [#417](https://github.com/storytold/designcraft/pull/417) (`11532d9`), written independently the same day, merged 2026-10-16 with the integration branch. Our own PR for it, [#400](https://github.com/storytold/designcraft/pull/400), is still open and can be closed. Its Objective-C code lives in the `fmv-macos-events` crate, so the repo's no-`unsafe` rule is kept. Fixes #211. |
| Copied objects paste on the spread in view | [#369](https://github.com/storytold/designcraft/pull/369), 2026-10-16 |
| Pages panel: double-clicking a parent shows that parent | [#635](https://github.com/storytold/designcraft/pull/635), 2026-10-16 |
| IDML text variables (running headers) | [#301](https://github.com/storytold/designcraft/pull/301), 2026-10-16 |
| Retry missing font fallbacks after a rescan | [#315](https://github.com/storytold/designcraft/pull/315), 2026-10-16 |
| Small frames (tombstones) set beside text wrap (ours) | [#589](https://github.com/storytold/designcraft/pull/589), 2026-10-15 |
| macOS text clipboard | [#349](https://github.com/storytold/designcraft/pull/349), 2026-10-15; `paste-without-formatting` (our ⇧⌘V follow-up) left out of `personal` on 2026-10-16 |
| A line is set when its baseline fits (what we took #106 for) | Upstream's own `d52ef6b6`, 2026-10-15 |
| Text editing, threading, 2–5 click selection; drop caps and first baselines | [#84](https://github.com/storytold/designcraft/pull/84) and [#83](https://github.com/storytold/designcraft/pull/83), 2026-10-13 (`0f4f382`) |
| Parent-page running headers by page index (ours) | [#523](https://github.com/storytold/designcraft/pull/523), 2026-10-12 (`a21e8db`) |

## Problems found but not fixed

- **"Page N" header text** (Def-e-nish IDML): needs the BarryGothicC font installed. Without it
  the fallback font's ascent puts the first baseline below the 11 pt frame.
- **Dictionary page layout differs from InDesign** (Def-e-nish, page 16): the left column starts
  halfway down and the footnotes sit differently. Not investigated.
- **The web build has only its built-in fonts** (Inter, Source Sans 3, Source Serif 4, JetBrains
  Mono): browsers don't let pages read installed fonts. Options discussed 2026-10-15 (load font
  files into the page; Chrome's Local Font Access); none built yet.
- **Copying objects between DesignCraft and other apps** isn't supported. Text copy and paste works.

- **Inline-picture lines are taller than InDesign's**: DesignCraft adds the part of an inline picture below the baseline (a negative Y offset) to the line's height; InDesign doesn't (its line is the picture's height above the baseline + 2.8 pt here). Def-e-nish page 7: 14 pt more space above the Flukemunschelfen picture.

## Not yet verified

- **On the Mac:**
  - `inline-objects-under-text`: dictionary page 7, *Foofie* is whole under the Flukemunschelfen picture.
  - `running-header-order`: in the dictionary, page 2's right header reads *Baas Roma*.
  - `parent-page-display`: open the parent spread with hidden characters shown: the headers read `<first on page>` and `<last on page>`, and the right page has no stray ¶ marks.
  - With DesignCraft open, install a font in Font Book, switch back: the font is in the font menu,
    and text set in it (shown in a fallback while it was missing) now uses it.
  - A drop cap set larger than the body text is taller than one set at the body's size.
  - Save documents as IDML: open an `.idml`, edit, ⌘S, reopen (in DesignCraft and in InDesign):
    the edit is there. A new document's ⌘S suggests `Untitled-1.idml`.
  - Upstream's Finder/Dock opening (#417), which replaced ours: double-click an `.idml`, drop one
    on the Dock icon, Open With.
  - `inline-object-select`: in the dictionary, click an image with the Selection tool: it shows its handles. Drag a handle: an outline and W/H follow; on release the frame resizes (left/top crop that side, then it snaps to the line) and the text reflows; ⌘-drag scales the picture; Undo restores it; Delete removes it.
  - `flexible-word-spacing`: open the dictionary IDML; page 1 ends at *Antefa* with the cat in column 1. Preferences › Composition › Word Spacing below Minimum shows *InDesign-compatible*; choosing *Never (strict)* reflows it as before, and Undo puts it back.
  - `idml-app-icon-drop`: after installing the rebuilt app, launch it once so macOS re-reads its
    file types; then dropping an `.idml` on the app's icon in Finder (or in Applications) opens it,
    and Open With lists DesignCraft.
- **The web build**: rebuild and repeat the 2026-10-15 browser check (Save documents as survives a
  reload; ⌘S downloads `.idml`), since upstream changed the download code underneath.
- **The wasm check** (`xtask wasm`) for `flexible-word-spacing`: the wasm target can't be
  downloaded here. The change is plain arithmetic in the line breaker, so the web build should
  compile; building the web package checks it.
- Everything else passed on Linux, on `personal` and on each branch above separately: fmt,
  clippy `-D warnings`, all workspace tests, `xtask layers` and `xtask assets`.

## Removing an item when upstream merges it

1. **Sync fork** on `main`, then merge `main` into `personal`.
2. Where both sides changed the same code, keep upstream's version and drop ours.
3. Remove the item's row from this file, and delete its branch once nothing else needs it.
