# CLAUDE.md — SBY RECO FLYER

This project produces the monthly "Evening of Recollection" flyer for Surabaya.

## Standing rules
- `Recollection Flyer.dc.html` is the source of truth. `support.js` is runtime — never edit it.
- For a new month's flyer, follow `skills/recollection-flyer/SKILL.md`. Only the **date** changes by default; **color scheme** and **illustration** change only when the user specifies them (illustration is described in words and drawn as SVG in the existing ink-sketch style).
- Everything else — layout, typography (Cormorant Garamond + EB Garamond), schedule, venue, quote — stays fixed unless explicitly requested.
- Final deliverable is always the retina JPG (2160×2880) via the export recipe in the skill, saved as `Evening of Recollection.jpg` and presented for download.
- Temp export files (`screenshots/`, timestamped `Retina *.dc.html` / `Recollection *.jpg`) must be deleted after export.
- Design size is fixed at 1080×1440; verify no overflow/wrapping before export (checks listed in the skill).
