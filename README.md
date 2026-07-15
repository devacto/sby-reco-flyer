# SBY RECO FLYER

Monthly "Evening of Recollection" flyer for Surabaya — an HTML design that exports to a retina JPG (2160×2880) for sharing on phones.

## What's in here

| Path | Purpose |
|---|---|
| `Recollection Flyer.dc.html` | The flyer design (1080×1440). Source of truth — open in a browser to preview. |
| `assets/illustration.png` | Current artwork (statue + walking figure), blended into the flyer background. |
| `assets/original.png` | The original reference flyer image. |
| `support.js` | Runtime needed by the `.dc.html` design. Do not edit. |
| `skills/recollection-flyer/SKILL.md` | The reusable recipe for generating next month's flyer. |
| `Evening of Recollection.jpg` | Latest exported retina JPG. |

## How to generate next month's flyer

1. Start a new Claude chat and attach/link this repo (or the local folder).
2. Say, for example:

   > Follow `skills/recollection-flyer/SKILL.md`.
   > Date: Wednesday, 26 August 2026.
   > Colors: deep green + ivory + copper. *(optional)*
   > Illustration: a becak driver resting under a banyan tree. *(optional — described in words, drawn in the same ink-sketch style)*

3. Claude updates the date (and colors/illustration if given), verifies the layout, and delivers a fresh `Evening of Recollection.jpg`.

Only the date changes by default — layout, typography, schedule, venue, and quote stay fixed unless explicitly requested.

## Flyer contents (fixed)

- **Event:** Evening of Recollection — Holiness in the Middle of the World, following the teachings of St. Josemaría
- **Venue:** Jl. Dukuh Kupang Barat XXXII No. 25, Surabaya · 7 PM · for gentlemen, in English
- **Schedule:** 18:30 Confessions · 19:00 Spiritual Reading · 19:15 Meditation I · 19:40 Personal Examination · 19:45 Talk · 20:10 Meditation II · 20:30 End
