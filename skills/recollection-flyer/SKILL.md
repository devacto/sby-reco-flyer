# Skill: SBY Recollection Flyer

Generate the monthly "Evening of Recollection" flyer for Surabaya (1080×1440 design, delivered as a 2160×2880 retina JPG).

## Source of truth
- `Recollection Flyer.dc.html` — the flyer design. ALL content stays exactly as-is except the parameters below.
- `assets/illustration.png` — the artwork on the right of the schedule (blended into the background via multiply + edge mask).

## Parameters
1. **date** (required) — e.g. "Wednesday, 22 July 2026". Update ONLY the date text in the info row:
   `Wednesday,<br>22 July 2026<br>at 7&nbsp;PM` → new weekday + date. Keep the 3-line break structure and `at 7 PM` unless told otherwise.
2. **color scheme** (optional) — if the user gives new colors, remap by ROLE across the whole file (find-and-replace every occurrence):
   - Background: `#F8F1E0` (root bg), `#FBF6EA` if present
   - Ink / headings: `#16264A`, `#1C2B4F`, body text `#27325A`, secondary `#34406A`
   - Accent (rules, icons, diamonds, SCHEDULE, subtitle, attribution): `#A8833C` and every `rgba(168,131,60,…)` (keep the same alphas)
   Keep contrast: dark ink on light bg; accent must read at 1px hairline weight. If user gives only 1–2 colors, derive the rest with oklch harmony rather than inventing unrelated hues.
3. **illustration** (optional) — the user describes the scene IN WORDS (e.g. "a fisherman mending nets at a harbor at dawn"). Claude draws it as an inline SVG in the same visual style as the current artwork:
   - Style: hand-sketched ink line drawing — loose, engraved/etched feel; no flat cartoon fills, no gradients-as-shading.
   - Palette: ONLY the flyer's ink + accent colors (default navy `#27325A`/`#16264A` lines with gold `#A8833C` accents) on transparent background, so it sits on the cream bg like ink on paper.
   - Composition: roughly square (~660×630 viewBox), one clear focal subject + minimal ground/context lines, generous whitespace; include a small human figure for scale where it fits the scene.
   - Technique: build from stroked paths (varying stroke-width 1–3, round caps), cross-hatching for shadow, few or no filled shapes.
   - Placement: replace the `<img>` with the SVG (or save as `assets/illustration.svg` and reference it) but KEEP the container's edge-fade mask styles so it blends into the flyer. Drop `mix-blend-mode:multiply` if the SVG has a transparent background (not needed).
   - Iterate: screenshot the illustration region, eyeball it, refine paths 2–3 rounds before export. If the scene is too complex to draw convincingly (faces, crowds, detailed architecture), say so and propose a simpler composition or ask for a reference image instead.

Everything else — layout, typography (Cormorant Garamond + EB Garamond), schedule times/events, venue, quote — DOES NOT CHANGE unless explicitly requested.

## Verify before export (eval on live preview)
- Root height exactly 1440, no overflow.
- No schedule event wraps or clips; the 1px divider between time and event is visible on every row (it has `flex:none` — keep it).
- Info-row columns are 3 lines each, centered, within bounds.
- "HOLINESS IN THE MIDDLE OF THE WORLD" stays on one line.

## Retina JPG export recipe (2160×2880)
The preview iframe is ~924×540, so capture by tiling a true-2× copy:
1. Make a temp 2× file with a **fresh timestamped name** (never reuse a filename — renders and viewers cache):
   - Double every `px` number: `src.replace(/(\d+(?:\.\d+)?)px/g, (m,n)=>(parseFloat(n)*2)+'px')`
   - Double the icon SVG attrs: `width="32" height="32"` → `width="64" height="64"` (viewBox stays).
   - Preview props 1080/1440 → 2160/2880.
2. Open it, confirm via eval: root is 2160 wide, scrollHeight 2880, new date present.
3. Capture 18 HQ PNG tiles, ONE save_screenshot call per tile (multi-step scroll captures return stale tiles — do not batch):
   - x offsets: 0, 924, 1236; y offsets: 0, 540, 1080, 1620, 2160, 2340; `window.scrollTo(x,y)` with ~400ms delay.
4. Stitch on canvas 2160×2880 (fill bg color first), draw each tile at its (x,y), export JPEG quality 0.95 to a **fresh timestamped filename**, then view to verify content (date, schedule, dividers, no seams).
5. Copy/move to `Evening of Recollection.jpg`, delete temp 2× file and `screenshots/`, present for download.

## Known pitfalls
- Filename reuse = stale cache (wrong image shown in viewer and captures). Always timestamp temp files.
- Long schedule labels overflow and crush the row divider — keep labels short enough for one line at 33px.
- The px-doubling regex misses unitless SVG width/height attributes — handle them explicitly.
