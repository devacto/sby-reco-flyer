# CLAUDE.md — SBY RECO FLYER

This project produces the monthly "Evening of Recollection" flyer for Surabaya.

## Standing rules
- `reco_flyer.dc.html` is the source of truth. `support.js` is runtime — never edit it.
- For a new month's flyer, follow `skills/recollection-flyer/SKILL.md`. Only the **date** changes by default; **color scheme** and **illustration** change only when the user specifies them (illustration is described in words and drawn as SVG in the existing ink-sketch style).
- Everything else — layout, typography (Cormorant Garamond + EB Garamond), schedule, venue, quote — stays fixed unless explicitly requested.
- Final deliverable is always the retina JPG (2160×2880) via the export recipe in the skill, saved as `reco_flyer.jpg` and presented for download.
- Temp export files (`screenshots/`, timestamped `Retina *.dc.html` / `Recollection *.jpg`) must be deleted after export.
- Design size is fixed at 1080×1440; verify no overflow/wrapping before export (checks listed in the skill).

## Git rules
- When making a git commit, never add a "Co-Authored-By: Claude" (or any "co-authored by") line to the commit message.
- Before any `git push`, check the commit messages being pushed and confirm none of them contain "Co-Authored-By: Claude"; if one does, amend/reword it first.
