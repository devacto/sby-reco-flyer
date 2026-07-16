# Skill: SBY Recollection Flyer

Generate the monthly "Evening of Recollection" flyer for Surabaya (1080×1440 design, delivered as a 2160×2880 retina JPG).

## Source of truth
- `reco_flyer.dc.html` — the flyer design. ALL content stays exactly as-is except the parameters below.
- `assets/illustration.png` — the artwork on the right of the schedule (blended into the background via multiply + edge mask).

## Parameters
1. **date** (required) — e.g. "Wednesday, 22 July 2026". Update ONLY the date text in the info row:
   `Wednesday,<br>22 July 2026<br>at 7&nbsp;PM` → new weekday + date. Keep the 3-line break structure and `at 7 PM` unless told otherwise.
2. **color scheme** (optional) — if the user gives new colors, remap by ROLE across the whole file (find-and-replace every occurrence):
   - Background: `#F8F1E0` (root bg), `#FBF6EA` if present
   - Ink / headings: `#16264A`, `#1C2B4F`, body text `#27325A`, secondary `#34406A`
   - Accent (rules, icons, diamonds, SCHEDULE, subtitle, attribution): `#A8833C` and every `rgba(168,131,60,…)` (keep the same alphas)
   Keep contrast: dark ink on light bg; accent must read at 1px hairline weight. If user gives only 1–2 colors, derive the rest with oklch harmony rather than inventing unrelated hues.
3. **illustration** (optional) — the user describes the scene IN WORDS (e.g. "a fisherman mending nets at a harbor at dawn"). Generate it with the **fal.ai MCP** using the **`fal-ai/nano-banana-2`** text-to-image model:
   - Call `mcp__fal-ai__run_model` with `endpoint_id: "fal-ai/nano-banana-2"` and input `{aspect_ratio: "1:1", resolution: "2K", output_format: "png", num_images: 1}` plus the prompt below.
   - Prompt template (swap in the scene and the flyer's CURRENT colors — read the ink, accent, and background hex values from `reco_flyer.dc.html` first; don't hardcode):
     > Hand-sketched ink line drawing in a loose engraved / etched vintage style: {scene}. The person radiates peace, tranquility and quiet joy — a serene, gently contented expression and a calm, upright, unhurried posture, as of someone whose ordinary work is done in union with God; never strained, hunched, anxious or rushed. Built entirely from expressive stroked pen lines of varying weight with cross-hatching for shadows — no flat cartoon fills, no gradient shading, no photorealism. Ink color is {ink hex, e.g. deep forest green #173E30}, with a few subtle {accent hex, e.g. copper-bronze #B06E2E} accent strokes on small details. Plain solid warm cream paper background ({bg hex}), completely empty — no border, no frame. ABSOLUTELY NO text, letters, words, numbers, labels or signatures anywhere in the image; book spines, signs and screens must be blank or abstract line marks only. Single focal subject centered with generous whitespace around it, minimal ground/context lines only. Roughly square composition, like an ink illustration printed on cream paper.
   - Download the returned image URL (curl) to the scratchpad, then Read it and eyeball: correct scene, ink-sketch style (no photorealism/flat fills), palette respected, and NO stray lettering anywhere (the model likes writing on book spines — regenerate with a firmer no-text clause if it does).
   - **Mood check (required):** the illustration must exude peace, tranquility and joy flowing from the person's union with God — serene visible face, gentle contentment, composed unhurried posture, expressed through ordinary means (expression, posture, light), NOT overt symbols (no halos, no floating icons). If the figure reads as strained, hunched, hurried or anxious, regenerate with the mood language strengthened before proceeding.
   - Normalize the background: the model's cream is never exactly the flyer's, and its paper has faint noise that shows as mottling after multiply. Per channel (Pillow `im.point(luts)`): divide by the sampled corner bg (`s = v*255/bg[ch]`), then clamp near-white to white with a smooth ramp (`s >= 248 → 255`, `230 < s < 248 → 230 + (s-230)*25/18`) so paper noise dies without a hard step at stroke edges. Verify an empty paper region is min-channel 255, not just the corners.
   - Save as `assets/illustration.png`, point the `<img>` at it and **bump the `?v=` query** (viewers cache), set the alt text to the scene, and KEEP both `mix-blend-mode:multiply` (the white paper vanishes into the flyer bg) and the container's edge-fade mask styles.
   - Verify the blend: multiply-composite the normalized image over the flyer bg color in Pillow and check the corners come out ≈ the flyer bg hex.

Everything else — layout, typography (Cormorant Garamond + EB Garamond), schedule times/events, venue, quote — DOES NOT CHANGE unless explicitly requested.

## Verify before export (eval on live preview)
- Root height exactly 1440, no overflow.
- No schedule event wraps or clips; the 1px divider between time and event is visible on every row (it has `flex:none` — keep it).
- Info-row columns are 3 lines each, centered, within bounds.
- "HOLINESS IN THE MIDDLE OF THE WORLD" stays on one line.

## Retina JPG export recipe (2160×2880)
**CLI environment (preferred when headless Chrome is available):** serve the project dir (`python3 -m http.server 8123`), capture in one shot with `"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --headless=new --disable-gpu --hide-scrollbars --window-size=1080,1440 --force-device-scale-factor=2 --virtual-time-budget=20000 --user-data-dir=<scratch>/chrome-profile --screenshot=<scratch>/flyer_2x.png http://127.0.0.1:8123/reco_flyer.dc.html` (Chrome may hang after writing the file — the PNG is still valid; kill it and check). Verify 2160×2880 and eyeball the capture (date, fonts loaded, dividers, illustration blend, no overflow), convert to JPEG quality 95 (`sips -s format jpeg -s formatOptions 95`) as `reco_flyer.jpg`, delete the temp PNG and chrome profile.

**Design-canvas environment (no headless Chrome):** the preview iframe is ~924×540, so capture by tiling a true-2× copy:
1. Make a temp 2× file with a **fresh timestamped name** (never reuse a filename — renders and viewers cache):
   - Double every `px` number: `src.replace(/(\d+(?:\.\d+)?)px/g, (m,n)=>(parseFloat(n)*2)+'px')`
   - Double the icon SVG attrs: `width="32" height="32"` → `width="64" height="64"` (viewBox stays).
   - Preview props 1080/1440 → 2160/2880.
2. Open it, confirm via eval: root is 2160 wide, scrollHeight 2880, new date present.
3. Capture 18 HQ PNG tiles, ONE save_screenshot call per tile (multi-step scroll captures return stale tiles — do not batch):
   - x offsets: 0, 924, 1236; y offsets: 0, 540, 1080, 1620, 2160, 2340; `window.scrollTo(x,y)` with ~400ms delay.
4. Stitch on canvas 2160×2880 (fill bg color first), draw each tile at its (x,y), export JPEG quality 0.95 to a **fresh timestamped filename**, then view to verify content (date, schedule, dividers, no seams).
5. Copy/move to `reco_flyer.jpg`, delete temp 2× file and `screenshots/`, present for download.

## Publish (required after every regeneration)
The user views the flyer from github.com, so the job is not done until the changes are pushed. After the export recipe finishes, without waiting to be asked:
1. `git commit` the changed files (`reco_flyer.dc.html`, `assets/illustration.png`, `reco_flyer.jpg`, plus anything else touched).
2. **Archive the generation** so the user can go back to any previous version: copy the fresh `reco_flyer.jpg` to `archive/YYYYMMDD-HHMM_<illustration-slug>.jpg` (local WIB export time, short kebab-case slug of the illustration scene) and append a row to the table in `archive/LOG.md`: generated time, event date, illustration description, colors, file link, and the hash of the commit from step 1.
3. `git commit` the archive file + `archive/LOG.md` (e.g. "Archive generation 20260716-1031 (factory-workers)"), then `git push` all commits. Never include a "Co-Authored-By: Claude" line, and check the commits being pushed for one before pushing (per CLAUDE.md git rules).

## Known pitfalls
- Filename reuse = stale cache (wrong image shown in viewer and captures). Always timestamp temp files.
- Long schedule labels overflow and crush the row divider — keep labels short enough for one line at 33px.
- The px-doubling regex misses unitless SVG width/height attributes — handle them explicitly.
